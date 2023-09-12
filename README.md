Travis Scott Burger Fantasy League
================

### Contents

- [Team Standings](#team-standings)
- [Points Scored Per Game](#points-scored-per-game)
- [Points Against Per Game](#points-against-per-game)

<details>
<summary>
View Code
</summary>

``` r
library(tidyverse)
library(tvthemes)
library(googlesheets4)

theme_custom = theme_avatar() +
  theme(plot.title = element_text(hjust = 0.5),
        plot.subtitle = element_text(hjust = 0.5, size = 9, vjust = 2.5, face = "italic"),
        plot.caption = element_text(face = "italic"),
        panel.grid.major = element_line(linewidth = 0.5, colour = "#DFDAD1"),
        panel.grid.minor = element_line(linewidth = 0.5, colour = "#DFDAD1"))

theme_set(theme_custom)
gs4_auth(email = "chadallison1251@gmail.com")
base = "https://docs.google.com/spreadsheets/"
rest = "d/1EtllzbCMieF43EHT6p0NWPj7bGglvJ-kV7q7eXtY1zA/edit?usp=sharing"
url = paste0(base, rest)
teams = read_sheet(url, sheet = 1)
games = read_sheet(url, sheet = 2)

end_games = games |>
  filter(!is.na(away_score) & !is.na(home_score)) |>
  mutate(win_team = ifelse(home_score > away_score, home_team, away_team),
         win_score = ifelse(home_score > away_score, home_score, away_score),
         lose_team = ifelse(home_score > away_score, away_team, home_team),
         lose_score = ifelse(home_score > away_score, away_score, home_score))

all_teams = sort(unique(c(unique(end_games$home_team), unique(end_games$away_team))))

team_records = data.frame(team = all_teams) |>
  left_join(teams, by = c("team" = "team_name")) |>
  separate(manager, into = c("manager", "last"), sep = " ") |>
  select(team, manager) |>
  left_join(end_games |>
  count(win_team) |>
  rename(team = win_team, wins = n), by = "team") |>
  left_join(end_games |>
  count(lose_team) |>
  rename(team = lose_team, losses = n), by = "team") |>
  mutate(wins = replace_na(wins, 0),
         losses = replace_na(losses, 0),
         record = paste0(wins, "-", losses),
         pct = round(wins / (wins + losses), 3),
         team_name = paste0(team, " (", manager, ")"))

standings_plot = team_records |>
  ggplot(aes(reorder(team_name, pct), pct)) +
  geom_col(fill = "#90AC8D") +
  geom_text(aes(label = record), size = 3, hjust = -0.25) +
  coord_flip(ylim = c(0, max(team_records$pct * 1.05))) +
  labs(x = NULL, y = "Win Percentage",
       title = "Team Standings") +
  scale_y_continuous(breaks = seq(0, 1, by = 0.1), labels = scales::percent)

get_pts_scored = function(team) {
  home = end_games |> filter(home_team == team) |> pull(home_score)
  away = end_games |> filter(away_team == team) |> pull(away_score)
  return(mean(c(home, away)))
}

pts_scored = data.frame(team = all_teams) |>
  left_join(teams, by = c("team" = "team_name")) |>
  separate(manager, into = c("manager", "last"), sep = " ") |>
  transmute(team, team_name = paste0(team, " (", manager, ")")) |>
  mutate(scored = sapply(team, get_pts_scored))

scored_plot = pts_scored |>
  ggplot(aes(reorder(team_name, scored), scored)) +
  geom_col(aes(fill = scored), show.legend = F) +
  geom_text(aes(label = scored), size = 3, hjust = -0.25) +
  coord_flip(ylim = c(0, max(pts_scored$scored * 1.05))) +
  scale_fill_gradient(high = "#82B47D", low = "#CF9696") +
  scale_y_continuous(breaks = seq(0, 150, by = 10)) +
  labs(x = NULL, y = "Points Scored Per Game",
       title = "Points Scored Per Game Standings",
       subtitle = "fuck you Eric")

get_pts_against = function(team) {
  home = end_games |> filter(home_team == team) |> pull(away_score)
  away = end_games |> filter(away_team == team) |> pull(home_score)
  return(mean(c(home, away)))
}

pts_against = data.frame(team = all_teams) |>
  left_join(teams, by = c("team" = "team_name")) |>
  separate(manager, into = c("manager", "last"), sep = " ") |>
  transmute(team, team_name = paste0(team, " (", manager, ")")) |>
  mutate(against = sapply(team, get_pts_against))

against_plot = pts_against |>
  ggplot(aes(reorder(team_name, against), against)) +
  geom_col(aes(fill = against), show.legend = F) +
  geom_text(aes(label = against), size = 3, hjust = -0.25) +
  coord_flip(ylim = c(0, max(pts_against$against * 1.05))) +
  scale_fill_gradient(low = "#82B47D", high = "#CF9696") +
  scale_y_continuous(breaks = seq(0, 150, by = 10)) +
  labs(x = NULL, y = "Points Against Per Game",
       title = "Points Against Per Game Standings",
       subtitle = "fuck you Eric")
```

</details>

------------------------------------------------------------------------

### Team Standings

![](README_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

------------------------------------------------------------------------

### Points Scored Per Game

![](README_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

------------------------------------------------------------------------

### Points Against Per Game

![](README_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->
