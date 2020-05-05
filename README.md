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


index <- 1
post_id <- list()
post_url <- list()
post_text <- list()
post_time <- list()
post_likes <- list()
post_owner <- list()
post_img_url <- list()

extractInfo <- function(index){
  print("extractInfo function called")
  maxrows <- nrow(posts)
  for(i in 1:maxrows){
    if(i == maxrows){
      assign("index", index, envir = .GlobalEnv)
      assign("post_id", post_id, envir = .GlobalEnv)
      assign("post_text", post_text, envir = .GlobalEnv)
      assign("post_time", post_time, envir = .GlobalEnv)
      assign("post_img_url", post_img_url, envir = .GlobalEnv)
      assign("post_url", post_url, envir = .GlobalEnv)
      assign("post_likes", post_likes, envir = .GlobalEnv)
      assign("post_owner", post_owner, envir = .GlobalEnv)
      getNewPosts(index)
    } else {
      if(length(posts$edge_media_to_caption$edges[[i]][["node"]][["text"]])==0){
        post_text[index] <- "no-text"
        print("no text in post")
      } else {
        temp <- posts$edge_media_to_caption$edges[[i]][["node"]][["text"]]
        post_text[index] <- gsub("\n", " ", temp)
      }
      
      post_id_temp <- posts[i,5]
      post_url[index] <-  str_glue("http://instagram.com/p/{post_id_temp}")
      post_id[index] <- post_id_temp
      post_time[index] <- toString(as.POSIXct(posts[i,7], origin="1970-01-01"))
      post_img_url[index] <- posts[i,9]
      post_likes[index] <- posts[i,11]
      post_owner[index] <- posts[i,12]
      
      #optional: download image
      img_dir <- str_glue("images/{index}_{hashtag}_post_img.jpg")
      download.file(posts[i,8], img_dir, mode = 'wb')
      
      index <- index + 1
    }
  }    
}
