---
layout: page
element: notes
title: for loops
language: R
---

> Set up R console:

```
library(stringr)
library(dplyr)
```

### Basic `for` loop

* Repeats action for a number of different values

```
for (item in list_of_items) {
  do_something(item)
}
```

* Make sure you know what values you are calling in your loop.

```
pets <- c("spot", "gigantor", "fluffy")
for (pet in pets) {
  print(pet)
}
```

* Need `print()` to display values inside a loop, function, or conditional.
* Long-form expression of the previous loop

```
pets <- c("spot", "gigantor", "fluffy")
pet <- pets[1]
print(pet)
pet <- pets[2]
print(pet)
pet <- pets[3]
print(pet)
```

* Do more things.

```
pets <- c("spot", "gigantor", "fluffy")
for (pet in pets) {
  class_pet <- paste(pet, "is the name of the class pet")
  print(class_pet)
}
```

> Do [Exercise 1 - for Loop]({{ site.baseurl }}/exercises/Functions-for-loop-R).

> Make sure students get the basics before moving on.

### Storing results

* Create an empty object.
    * `output <- c()`
    * `output <- data.frame()`
    * `output <- data.frame(name = character(3))`
* Add the new values each trip through the loop.

```
pets <- c("spot", "gigantor", "fluffy")
output <- data.frame()
for (pet in pets) {
  pet_upper <- str_to_upper(pet)
  output <- rbind(output, data.frame(name = pet_upper,
                          namelength = str_length(pet_upper)))
}
```

### Looping in data frames

* By default data frames loop over columns

```
pets <- data.frame(pet_name = c("spot", "gigantor", "fluffy"),
                   pet_type = c("fish", "hamster", "lizard"))
for (pet in pets){
  print("Start new loop")
  print(pet)
}
```


* To loop over rows we need to loop over an `index` for the row number

```
pets <- data.frame(pet_name = c("spot", "gigantor", "fluffy"),
                   pet_type = c("fish", "hamster", "lizard"))
for (i in 1:nrow(pets)){
  print(paste(pets$pet_name[i], " is a ", pets$pet_type[i], sep =""))
}
```

* We can also use the index to store the output by pre-allocating memory
* This is a lot faster than using `rbind` because it doesn't copy the data frame
  every time through the loop

```
output <- data.frame(name = character(3), namelength = numeric(3),
                     stringsAsFactors = FALSE)
for (i in 1:nrow(pets)) {
  pet_upper <- str_to_upper(pets$pet_name[i])
  pet_length <- str_length(pets$pet_type[i])
  output[i,] <- c(pet_upper, pet_length)
}
```

> Assign [Exercise 2 - stringr]({{ site.baseurl }}/exercises/Loops-stringr-R).
> Assign [Exercise 3 - DNA or RNA]({{ site.baseurl }}/exercises/Making-choices-dna-or-rna-R).


### Alternate loops

* [`apply()`](http://finzi.psych.upenn.edu/R/library/base/html/apply.html)
    * Short cut for simple loops over rows and columns
    * Other versions allow you various control options
        * [`lapply()`](http://finzi.psych.upenn.edu/R/library/base/html/lapply.html): Operate across lists and vectors
        * [`sapply()`](http://finzi.psych.upenn.edu/R/library/base/html/lapply.html): Simplify output to vector
        * [`mapply()`](http://finzi.psych.upenn.edu/R/library/base/html/mapply.html): Pass multiple variables or function arguments
    * Why use `apply()`:
        * Readability
            * Single line of code
            * Simple command structure
        * Speed?
            * Noticeable in complex operations ([some debate](https://stackoverflow.com/questions/2275896/is-rs-apply-family-more-than-syntactic-sugar))
            * [Avoid premature optimization](http://c2.com/cgi/wiki?PrematureOptimization).

```
get_mass_from_length_theropoda <- function(length) {
  mass <- 0.73 * length ** 3.63
  return(mass)
}

lengths = c(5, 10, 15)
lapply(lengths, FUN=get_mass_from_length_theropoda)
sapply(lengths, FUN=get_mass_from_length_theropoda)
```

* Many functions also work with vectors
    * No need to loop through the vector's values

```
get_mass_from_length_theropoda(lengths)
```

* `dplyr`
    * Must have tidy data
    * Use `group_by()` and `summarize()`

```
biomass_data <- data.frame(experiment = c(1, 1, 1, 2, 2, 2, 3, 3, 3),
                           biomass = c(24, 32, 62, 10, 9, 5, 1, 5, 3))

biomass_data %>%
  group_by(experiment) %>%
  summarize(total_npp = sum(19.3 * biomass ** 2))
```