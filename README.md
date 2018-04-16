
<!-- README.md is generated from README.Rmd. Please edit that file -->
threadpool
==========

The `threadpool` package implements a simple parallel programming backend using a thread pool model that can be dynamically resized during operation. The package builds clusters by setting up a queue of input jobs and having the individual cluster nodes retrieve jobs from the queue one at a time. The queue is stored on-disk and nodes communicate with each other via the filesystem.

A few benefits of this model of parallel computing are

-   *Dynamic resource allocation*: You can modify the number of resources dedicated to a job as the job is running and as resources become available or unavailable.

-   *Automatic load balancing*: Tasks are not prescheduled to specific nodes of the cluster and so long-running jobs do not prevent faster-running jobs from executing.

-   *Fault tolerance and restartability*: Tasks can continue executing as nodes come in and out of the cluster. In particular, if the entire cluster goes down, the job can be automatically restarted where it left off.

-   *Seamless switching to parallel mode*: It's easy to start and debug a job in single processor mode and then ramp up to parallel computing without needing to start the job over again.

This package will be most useful in situations where the jobs being run

-   are embarassingly parallel, and so don't require complex communication between nodes;

-   are relatively long-running, on the order of hours or days;

-   have individual tasks that are heterogeneous in their runtimes.

Installation
------------

You can install threadpool from GitHub with:

``` r
# install.packages("remotes")
library(remotes)
install_github("rdpeng/threadpool")
```

The `threadpool` package makes use of the `thor` package by Rich FitzJohn. This package can be installed from GitHub with:

``` r
library(remotes)
install_github("richfitz/thor")
```

In addition, you need to install the `queue` package from GitHub with:

``` r
install_github("rdpeng/queue")
```

Example
-------

This is a basic example of how you might invoke the `threadpool` package. The primary user-facing function is `tp_map()`, which is can be thought of as a parallel version of `Map()`.

``` r
library(threadpool)
library(queue)

## Use the `tp_map()` function

data(airquality)
clname <- tp_map(airquality, function(x, meta) {
        list(output = mean(x, na.rm = TRUE))
})

cl <- cluster_join(clname)
peek(cl$outjob)
#> [1] 42.12931
```
