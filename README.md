# Visualizing unemployment trends collected by [@layoffbot](https://twitter.com/layoffbot).

<p align="center">
  <img src="https://github.com/imjakedaniels/layoffbot_timeseries/blob/master/covid_tracking_log10.png">
</p>

### Required Packages

```r
library(tidyverse)
library(rtweet)
library(lubridate)
library(ggiraph)
library(extrafont)
library(ggtext)
```

### Get tweets by the bot

```r
layoff_timeline <- get_timeline("layoffbot", n = 3200, include_rts = FALSE)
```

### Clean to get the lay off count in each tweet

```r
layoff_df <- layoff_timeline %>%
  filter(str_detect(text, "In the last hour,")) %>%
  select(created_at, text) %>%
  mutate(created_at = ymd_hms(created_at),
         laid_off_count = as.numeric(str_extract(text, "[0-9]+")))
```

### Base Chart

```r
p2 <- layoff_df %>%
  ggplot(aes(x = created_at, y = laid_off_count)) +
  geom_line(colour = "purple") +
  scale_x_datetime(breaks = scales::date_breaks("1 day"),
                   labels = scales::date_format("%a. \n %b \n %d"), position = "top") +
  scale_y_log10(position = "right") +
  labs(title = "Tracking Self-Declared Layoffs During COVID-19",
       subtitle = 'Estimating recently unemployed workers by counting tweets with the words <br><i><b>"I was just laid off" </i></b> / <i><b> "I just got laid off"</i></b>',
       x = "",
       y = "Tweets Per Hour",
       caption = "SOURCE: @layoffbot; VIZ: @datajake") +
  theme_minimal() +
  theme(text = element_text(family = "Roboto Condensed"),
        panel.grid.minor = element_blank(),
        panel.grid.major.x = element_blank(),
        axis.text.x.bottom = element_text(),
        plot.margin = unit(c(0.5,0.5,0.5,0.7), units = "cm"),
        plot.title = element_text(hjust = 0.5, family = "Merriweather ExtraBold", colour = "purple"),
        plot.subtitle = element_markdown(hjust = 0.7, halign = 0.5, lineheight = 1.5),
        plot.caption = element_text(colour = "grey50", hjust = 0),
        axis.title.y.right = element_text(size = 8),
        axis.ticks.x = element_line(color = "grey50"))
```

### SVG

```r
girafe(ggobj = p2, width_svg = 7.2, height_svg = 4, 
       opts_toolbar(saveaspng = FALSE))
```
