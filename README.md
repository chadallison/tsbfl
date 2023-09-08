
### Contents

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
```

### Data Import

``` r
gs4_auth(email = "chadallison1251@gmail.com")
base = "https://docs.google.com/spreadsheets/"
rest = "d/1EtllzbCMieF43EHT6p0NWPj7bGglvJ-kV7q7eXtY1zA/edit?usp=sharing"
url = paste0(base, rest)
teams = read_sheet(url, sheet = 1)
games = read_sheet(url, sheet = 2)
```
