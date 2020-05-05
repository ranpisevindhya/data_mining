# data_mining
library(jsonlite)
library(stringr)
library("jpeg")
library(tidyr)
library(utf8)

hashtag <- "trek"
url_start <- str_glue("http://instagram.com/explore/tags/{hashtag}/?__a=1")
json <- fromJSON(url_start)
edge_hashtag_to_media <- json$graphql$hashtag$edge_hashtag_to_media
end_cursor <- edge_hashtag_to_media$page_info$end_cursor
posts <- edge_hashtag_to_media$edges$node
