# Example R script for processing UXF data

Here is a commented example of an R script (written in [Tidyverse](http://tidyverse.org/) style) that will process the data for a typical UXF task.

```r
library(tidyverse)

# assume our data is stored in a folder called `data` in our R project.
# within the `data` folder is our experiment folder (i.e. name of the experiment settings profile)
# there can be multiple experiment folders, maybe you use the settings profiles for different conditions.
# within the experiment folders, is each participant folder.
# within those, is the session folder for each participant.

# e.g: 
# project_folder/
# └── data/
#     ├── experiment_condition_1
#     │   ├── Participant01
#     │   │   ├──S001
#     │   │   │  └── (all UXF data files)
#     │   │   └──S002
#     │   │      └── (all UXF data files)
#     │   └── Participant02
#     │       ├──S001
#     │       │  └── (all UXF data files)
#     │       └──S002
#     │          └── (all UXF data files)
#     └── experiment_condition_2
#         ├── Participant03
#         │   ├──S001
#         │   │  └── (all UXF data files)
#         │   └──S002
#         │      └── (all UXF data files)
#         └── Participant04
#             ├──S001
#             │  └── (all UXF data files)
#             └──S002
#                └── (all UXF data files)


# first, we load all the trial_results files.
# we do this by recursively searching all files that match a pattern.
# then reading all the files we find (with `map`) and binding them row-wise (`_dfr`)

posture_trials <- list.files(
  path = "data",
  pattern = "trial_results.csv",
  full.names = TRUE,
  recursive = TRUE
  ) %>%
  map_dfr(read_csv)
  
# thats all our behavioural data!

# now if we want any movement data for each trial,
# we can do this by reading each filename given in the `*_movement_filename` column.
# then we can use `unnest` to give 1 row per timestep. 
# (in this example, `center_eye_movement_filename`)

posture_movement <- posture_trials %>% 
  mutate(center_eye_movement = map(file.path("data", directory, center_eye_movement_filename), read_csv)) %>% 
  unnest()

# now we have 1 row per timestep.

# we can now write a function which calculates (for example) path length and summarise by it.
# grouping will give us 1 row per trial again

calculate_path_length <- function(x, y, z) {
  # calculates sum of point-to-point distances.
  sum(
    (diff(x) ^ 2 +
     diff(y) ^ 2 +
     diff(z) ^ 2) ^ 0.5
    )
}

posture_summary <- posture_movement %>% 
  group_by(experiment, ppid, session_num, trial_num, assessment_type) %>% 
  summarise(path_length = calculate_path_length(pos_x, pos_y, pos_z))

# that outputs 1 row per trial, with our new movement summary summary statistic column.
```