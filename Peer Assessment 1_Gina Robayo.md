---
title: "Reproducible Research: Peer Assessment 1_Gina Robayo.md"
output: 
  html_document:
    keep_md: true
---


## Loading and preprocessing the data
activity = read.csv("C:/Users/ginna.robayo/Desktop/GINA PERSONAL/COURSERA/specdata/activity.csv")

## What is mean total number of steps taken per day?
total_of_steps_of_day <- sum(activity$steps, na.rm = TRUE)

##the total number of steps per day is
total_of_steps_of_day

##570608

##Make a histogram of the total number of steps taken each day
