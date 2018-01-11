# Getting Started with Docker and R

To get started with docker you need to install a couple peices of software.

Install docker
https://www.docker.com/docker-mac

Ok now you are set to start using docker.  Open the application and make sure that the whale is running (upper right next to the wifi button if on a Mac)

## Using the official ubuntu image:
Generally speaking R based docker files will build on top of rocker builds 

```{bash}
FROM rocker/rstudio
```

When you start building an image you can use FROM to start layering on top of an already existing image.  I usually use this image from rocker that installs the latest version of R and RStudio. This will pull the image from docker.io and give us a head start.  Here is the link for you reference https://hub.docker.com/r/rocker/rstudio/

The preamble is not super useful but it can help us know who created the image if any questions arise. The next peice is usually used to install all of the linix libraries that are needed for our R Package to function.  Most of it is boilerplate code but the apt-get install lines can be used to prepare some base linux installs that some packages might need.  Then we clean stuff up a bit before moving on to the next step

```{bash}
## Preamble:
MAINTAINER Tyler Hunt <thunt@snapfinance.com>

## Prepare apt-get:
RUN echo "debconf debconf/frontend select Noninteractive" | debconf-set-selections && \
	apt-get update -qy && \
  apt-get upgrade -qy && \
  apt-get install -qy littler libpq-dev libxml2-dev git && \
  apt-get clean autoclean && \
  apt-get autoremove -y && \
  apt-get clean
```

Now it is time to install the R packages. This should include the package you need for you analysis

```{bash}
## R packages
RUN R -e "install.packages('devtools', repos = 'http://cran.r-project.org')"
RUN R -e "install.packages(c('data.table'), repos = 'http://cran.r-project.org')"
```


Now we end the Dockerfile with this command

```{bash}
CMD ["R"]
```

So in sum here is the Dockerfile

```{bash}
## Using the official ubuntu image:
FROM rocker/rstudio

## Preamble:
MAINTAINER Tyler Hunt <thunt@snapfinance.com>

## Prepare apt-get:
RUN echo "debconf debconf/frontend select Noninteractive" | debconf-set-selections && \
	apt-get update -qy && \
  apt-get upgrade -qy && \
  apt-get install -qy littler libpq-dev libxml2-dev git && \
  apt-get clean autoclean && \
  apt-get autoremove -y && \
  apt-get clean

## R packages
RUN R -e "install.packages('devtools', repos = 'http://cran.r-project.org')"
RUN R -e "install.packages(c('data.table'), repos = 'http://cran.r-project.org')"

CMD ["R"]
```
