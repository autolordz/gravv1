---
title: '第一次提交 Rpub 资源'
media_order: 1.html
published: true
date: '11/01/2017 04:57 pm'
metadata:
    Keywords: 'Blog, Grav, Rstudio,R,Math,Python,Blogger'
    author: 'Autoz Zhao'
taxonomy:
    category:
        - blog
    tag:
        - Rstudio
        - R
        - Coursera
        - Statistics
        - Model
        - Project
        - Blog
        - Grav
        - 'Machine Learning'
        - Predict
        - 分析
        - CMS
sitemap:
    changefreq: daily
    priority: 0.5
external_links:
    process: true
    no_follow: true
image:
    summary:
        enabled: '1'
        file: dstb_2019.png
summary:
    enabled: '1'
    format: short
author: Autoz
content:
    items: '@self.children'
    leading: 0
    columns: 2
    limit: 5
    order:
        by: date
        dir: desc
    show_date: false
    pagination: true
    url_taxonomy_filters: true
---

### 这是我第一个R assignment of Coursera, 是关于如何操作矩阵数据的

This two functions below are used to create a special object that stores a numeric matrix and cache's its inverse makeCacheMatrix creates a list containing a function to:

1. set the value of the matrix
2. get the value of the matrix
3. set the value of inverse of the matrix
4. get the value of inverse of the matrix

<pre><code class="r">
makeCacheMatrix <- function(x = matrix()) {
  m <- NULL
  set <- function(y) {
    x <<- y
    m <<- NULL
  }
  get <- function() x
  setmatrix <- function(matrix) m <<- matrix
  getmatrix <- function() m
  list(set=set, get=get, setmatrix=setmatrix, getmatrix=getmatrix)
}
</code></pre>

 The following function return a inverse of special "matirx" created with the above function 
 However, it first checks to see if the inverse has already been calculated.
 If so, it gets the inverse from the cache and skips the computation.
 Otherwise, it calculates the inverse of the matrix and sets the value of the inverse in ## the cache via the set_inverse function.

<pre><code class="r">
cacheSolve <- function(x, ...) {
  m <- x$getmatrix()
  if(!is.null(m)) {
    message("getting cached data")
    return(m)
  }
  data <- x$get()
  m <- solve(data, ...)
  x$setmatrix(m)
  m
}
</code></pre>

### 结果

![结果](1.html?lightbox=600,400&resize=200,200)


THE END