# HW07: Film Shot Length and Attention

For the final project, I am using the datasets of “Shot durations for 220 popular movies from 1915 to 2015” from James E. Cutting, a Psychology Professor at Cornell University. (http://people.psych.cornell.edu/~jec7/data.htm) File "Movies1915to2015" contains all data files downloaded from Cutting's website.

Please view the following parts for the detailed report: 
* Part 1: See `Project.Rmd`, where I include all codes for accessing the data files, generating the plot, and reports. 
* Part 2: See `Project.md` for plots and analysis texts in a presentation fashion.


## Reflections

I found this project really interesting. As a film lover, I have observed the trend when I compare and contemplate my personal experience of watching films. I am always interested in various editing styles to investigate how filmmakers use continuity to manipulate the audience’s psychological states, and I was happy when I caught Cutting’s dataset.  

One difficulty I met in this project was the starting point when I wanted to access all data files. Because those files end with different Excel formats and my RWorkbench only recognized files ending with “.xlsx,” I have to figure out a way to change all the data files’ names ending the same. I struggled for a while looking for solutions and finally found the function “lapply” could help. Other than that, I also found the original datasets to be unorganized in that they did not consistently store information and stowed all kinds of information in one file. 
