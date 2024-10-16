### Figures modified following reviewers' comments
# Manuscript figure

# rm(list = ls())

# packages
pkgs <- c("here", "dplyr", "ggplot2", "mgcv", "gratia", "scales", "tidyverse", "purrr",
          "gridExtra", "cowplot", "ggsci", "viridis", "pracma", "patchwork")
vapply(pkgs, library, logical(1L), logical.return = TRUE, character.only = TRUE)

source.files <- list.files(here("r"), full.names = TRUE)
sapply(source.files, source, .GlobalEnv)

load(here("Data/Empirical_example.RData"))
# the above loads all these old gratia functions - you should use the dev
# version of gratia and install the binary from my r-universe
# Now I delete all the old functions that are stored in the sourced image
rm(list = ls(getNamespace("gratia"), all.names = TRUE))

############ Figure 1 ################
E1_series <- seq(15, 40, 0.2)
E2_series <- seq(0.01, 30, 0.2)
# create a dataframe with E1 and E2 changing over time
set.seed(64)


# Set the number of time points
n <- 10

# Sample 8 random values from E1_series and E2_series
set.seed(123)  # Set seed for reproducibility
E1_random <- sample(E1_series, n - 2, replace = TRUE)
E2_random <- sample(E2_series, n - 2, replace = TRUE)

# Combine random values with min and max values
E1_combined <- c(min(E1_series), E1_random, max(E1_series))
E2_combined <- c(min(E2_series), E2_random, max(E2_series))

# Shuffle the values so they fluctuate randomly
E1_combined <- sample(E1_combined)
E2_combined <- sample(E2_combined)

# Create a time series data frame
refs_ts <- data.frame(
  Time = 1:n,
  E1 = E1_combined,
  E2 = E2_combined
)



refs_ts <- refs_ts%>% 
  dplyr::mutate(time = seq.int(nrow(refs_ts)))

p_E1 <- refs_ts %>% 
  ggplot(aes(x = time, y = E1)) + geom_line(color = "orange", size = 1.5) +
  geom_point(size = 3)+
  theme_classic(base_size = 30) +
  labs(y = "Temperature (°C)", x = "Time", tag = "(a)") 

p_E2 <- refs_ts %>% 
  ggplot(aes(x = time, y = E2)) + geom_line(color = "blue", size = 1.5) +
  geom_point(size = 3)+
  theme_classic(base_size = 30) +
  labs(y = "Phosphate concentration  (μmol L−1)", x = "Time",tag = "(b)") 

# Plot environmental change over time
p_E1 + p_E2
indipendent_fluctuations <- p_E1 + p_E2




### Plot species response surface 

# Sp 1 
Ankistrodesmus <- filter(nested_gams, SpeciesName == "Ankistrodesmus")


m_Ankistrodesmus <- (Ankistrodesmus$gams[[1]])

# draw te(x,z)
sp1 <- draw(m_Ankistrodesmus, rug = FALSE, dist = 0.5) &
  labs(title = "Ankistrodesmus", x = "Temperature", y = "Phosphate concentration", tag = "(a)") 

pd_Ankistrodesmus <- get_partials(m_Ankistrodesmus, refs_ts)





# calculation next value for directional derivatives, and get directional derivatives
(pd_Ankistrodesmus <- pd_Ankistrodesmus %>% transform( nxt_value_E1 = c(E1[-1], NA)) %>%
    transform(nxt_value_E2 = c(E2[-1], NA)) %>%
    dplyr::mutate(del_E1 = nxt_value_E1 - E1,
                  del_E2 = nxt_value_E2 - E2,
                  unit_vec_mag =  sqrt(del_E1^2 + del_E2^2),
                  uv_E1 = del_E1 / unit_vec_mag,
                  uv_E2 = del_E2 / unit_vec_mag,
                  dir_deriv = pd_E1 * uv_E1 +  pd_E2 * uv_E2) %>% 
    filter(time != max(time)))




# reduce the dataframe and keep only what we need
pd_Ankistrodesmus <- pd_Ankistrodesmus %>%  dplyr::select( time, E1, E2, dir_deriv)



dir_plot <- ggplot()+  geom_line(data = pd_Ankistrodesmus, aes(x = time + 0.5, y = dir_deriv), color = "black", size = 1) +
  geom_point(data = pd_Ankistrodesmus, aes(x = time + 0.5, y = dir_deriv), color = "black", size = 3.5) +
  scale_x_continuous(breaks = seq(0, 10, 1), expand = c(0, 0.5)) +
  geom_point(size = 3)+
  labs(x = "Time",y = "Directional derivative", tag = "(d)") +
  geom_hline(yintercept = 0, linetype= "dashed") + theme_classic(base_size = 30) +
  scale_x_continuous(breaks=seq(0, 10, 1))


# View combined plot
dir_plot

# Sp response surface

sp_plot <- sp1 + geom_point(data = refs_ts, mapping = aes(x = E1, y = E2), size = 3) +
  geom_path(data = refs_ts, size = 1.5) +
  geom_point(data = refs_ts, mapping = aes(x = E1, y = E2), size = 1.5) +
  geom_path(data = refs_ts,  size = 0.5, alpha = 0.5) + guides(color="none" , fill=guide_colourbar(title="Growth \n Rate")) +
  geom_label(data = refs_ts, mapping = aes(label = time), size = 10) + labs(x = "Temperature (°C)", y = "Phosphate concentration (μmol L−1)",
                                                                            title = "", tag = "(c)", caption = "")+ theme_classic(base_size = 30) #+ ylab("Salinity (ppt)") + xlab("Temperature (k)")  + 



Fig1 <- (p_E1 + p_E2 + sp_plot) / dir_plot
ggsave(Fig1, file = here("Revision/figure_revision/Fig1.jpg"), width = 22, height = 15)
ggsave(Fig1, file = here("Revision/figure_revision/Fig1.png"), width = 22, height = 15)
ggsave(Fig1, file = here("Revision/figure_revision/Fig1.pdf"), width = 22, height = 15)

########### Figure 2 ###############
# draw te(x,z)

sp1 <- draw(m_Ankistrodesmus, rug = FALSE, dist = 0.5) &
  labs(title = "Ankistrodesmus", x = "Temperature (°C)", y = "Phosphate concentration (μmol L−1)", tag = "(a)", caption = "") &
  ylim(4, 30) & xlim(15, 40) 


# Sp2 
Monoraphidium <- filter(nested_gams, SpeciesName == "Monoraphidium")
m_Monoraphidium <- (Monoraphidium$gams[[1]])

# draw te(x,z)
sp2 <- draw(m_Monoraphidium, rug = FALSE, dist = 0.5) &
  labs(title = "Monoraphidium", x = "Temperature", y = "Phosphate concentration", tag = "(b)", caption = "") &
  ylim(4, 30) & xlim(15, 40) 



### Sp1 surface - 1 point
refs <- crossing(E1 = seq(15, 40, 2),
                 E2 = seq(5, 30, 2))

refs_1 <- get_partials(m_Ankistrodesmus, refs) %>% 
  dplyr::mutate(E1_ref = 35, E2_ref = 20) 

radius <- 4
num_arrows <- 20
dd1 <- tibble(angle = rep(seq(0, 2*pi, length = num_arrows), nrow(refs_1))) %>%
  mutate(E1_ref = rep(refs_1$E1_ref, each = num_arrows),
         E2_ref = rep(refs_1$E2_ref, each = num_arrows)) %>%
  full_join(refs_1) %>%
  mutate(E1 = cos(angle) * radius,
         E2 = sin(angle) * radius,
         dir_deriv = E1 * pd_E1 + E2 * pd_E2,
         unit_vec_mag = sqrt(E1^2 + E2^2))
fi2_a <- sp1 +
  # Black outline segments (slightly larger size for the outline effect)
  geom_segment(data = dd1,
               aes(x = E1_ref, y = E2_ref,
                   xend = E1_ref + E1, yend = E2_ref + E2),
               color = "black", alpha = 1, size = 3.5) +  # Thicker black line for outline
  geom_segment(data = dd1,
               aes(x = E1_ref, y = E2_ref,
                   xend = E1_ref + E1, yend = E2_ref + E2,
                   col = dir_deriv), alpha = 1,
               size=2.5) +
  scale_colour_gradient2(low = muted("blue"),
                         mid = "white",
                         high = muted("red"),
                         midpoint = 0) +
  theme(
    axis.text = element_text(color = "black"),  # Set axis text to black
    axis.ticks = element_line(color = "black")  # Set axis ticks to black
  ) 


## Fig 2 a - one point

fig2_a <- fi2_a + 
  labs(tag = "(a)") + 
  guides(fill = guide_colourbar(title = "Growth \n Rate")) +
  theme(
    legend.key.width = unit(0.28, "cm"),  # Adjust width of the legend key
    legend.key.height = unit(0.28, "cm")
  ) + 
  labs(color = "Directional \n  Derivative", title = "Ankistrodesmus", x = "Temperature (°C)", y = "Phosphate concentration (μmol L−1)") +
  theme_classic(base_size = 30) +
  theme(
    plot.title = element_text(hjust = 0.5, vjust = 1, face = "bold")  # Center and adjust the title
  )

## Fig 2 c - grid of points
refs <- crossing(E1 = seq(15, 40, 2),
                 E2 = seq(5, 30, 2))

refs_2 <- get_partials(m_Ankistrodesmus, refs) %>% 
  dplyr::mutate(E1_ref = E1, E2_ref = E2) 

radius <- .8
num_arrows <- 20
dd1 <- tibble(angle = rep(seq(0, 2*pi, length = num_arrows), nrow(refs_2))) %>%
  mutate(E1_ref = rep(refs_2$E1_ref, each = num_arrows),
         E2_ref = rep(refs_2$E2_ref, each = num_arrows)) %>%
  full_join(refs_2) %>%
  mutate(E1 = cos(angle) * radius,
         E2 = sin(angle) * radius,
         dir_deriv = E1 * pd_E1 + E2 * pd_E2,
         unit_vec_mag = sqrt(E1^2 + E2^2))

p1.2 <-draw(m_Ankistrodesmus, rug = FALSE, dist = 0.5, n_contour = 10,
            contour_col = "black") &
  labs(title = "Ankistrodesmus", x = "Temperature (°C)", y = "Phosphate concentration (μmol L−1)", tag = "(a)", caption = "") &
  ylim(4, 30) & xlim(15, 40) &
  scale_fill_gradient(low = "white", 
                      high = "darkgrey") & 
  labs(title = "", fill = "Growth", caption = "") & theme_classic(base_size = 20) 


fig2_c <- p1.2 +
  # Black outline segments (slightly larger size for the outline effect)
  geom_segment(data = dd1,
               aes(x = E1_ref, y = E2_ref,
                   xend = E1_ref + E1, yend = E2_ref + E2),
               color = "black", alpha = 0.7, size = 2) +  # Thicker black line for outline
  geom_segment(data = dd1,
               aes(x = E1_ref, y = E2_ref,
                   xend = E1_ref + E1, yend = E2_ref + E2,
                   col = dir_deriv), alpha = 0.7,
               size=1) +
  scale_colour_gradient2(low = muted("blue"),
                         mid = "white",
                         high = muted("red"),
                         midpoint = 0) + 
  labs(x = "Temperature (°C)", y = "Phosphate concentration (μmol L−1)", tag = "(c)")+
  ylim(4, 30) + xlim(15, 40) +
  theme_classic(base_size = 30) +
  theme(
    axis.text = element_text(color = "black"),  # Set axis text to black
    axis.ticks = element_line(color = "black")  # Set axis ticks to black
  )


### Fig 2e
refs <- crossing(E1 = seq(18, 40, 8),
                 E2 = seq(7, 30, 10))

refs_2 <- get_partials(m_Ankistrodesmus, refs) %>% 
  dplyr::mutate(E1_ref = E1, E2_ref = E2) 


radius <- 2.5
num_arrows <- 4
dd1 <- tibble(angle = rep(seq(0, 2*pi, length = num_arrows), nrow(refs_2))) %>%
  mutate(E1_ref = rep(refs_2$E1_ref, each = num_arrows),
         E2_ref = rep(refs_2$E2_ref, each = num_arrows)) %>%
  full_join(refs_2) %>%
  mutate(E1 = cos(angle) * radius,
         E2 = sin(angle) * radius,
         dir_deriv = E1 * pd_E1 + E2 * pd_E2,
         unit_vec_mag = sqrt(E1^2 + E2^2))

fig2_e<- p1.2 +
  # Black outline segments (slightly larger size for the outline effect)
  geom_segment(data = dd1,
               aes(x = E1_ref, y = E2_ref,
                   xend = E1_ref + E1, yend = E2_ref + E2),
               color = "black", alpha = 1, size = 4.5) +  # Thicker black line for outline
  geom_segment(data = dd1,
               aes(x = E1_ref, y = E2_ref,
                   xend = E1_ref + E1, yend = E2_ref + E2,
                   col = dir_deriv), alpha = 1,
               size=3.5) +
  geom_point(data = dd1,
             aes(x = E1_ref, y = E2_ref,
                 size = 3))+
  scale_colour_gradient2(low = muted("blue"),
                         mid = "white",
                         high = muted("red"),
                         midpoint = 0) + 
  labs(x = "Temperature (°C)", y = "Phosphate concentration (μmol L−1)", tag = "(e)")+
  ylim(4, 30) + xlim(15, 40) +
  theme_classic(base_size = 30) +
  theme(
    axis.text = element_text(color = "black"),  # Set axis text to black
    axis.ticks = element_line(color = "black")  # Set axis ticks to black
  )




### Sp2 surface - 1 point
refs <- crossing(E1 = seq(15, 40, 2),
                 E2 = seq(5, 30, 2))

refs_1 <- get_partials(m_Ankistrodesmus, refs) %>% 
  dplyr::mutate(E1_ref = 35, E2_ref = 20) 

radius <- 4
num_arrows <- 20
dd1 <- tibble(angle = rep(seq(0, 2*pi, length = num_arrows), nrow(refs_1))) %>%
  mutate(E1_ref = rep(refs_1$E1_ref, each = num_arrows),
         E2_ref = rep(refs_1$E2_ref, each = num_arrows)) %>%
  full_join(refs_1) %>%
  mutate(E1 = cos(angle) * radius,
         E2 = sin(angle) * radius,
         dir_deriv = E1 * pd_E1 + E2 * pd_E2,
         unit_vec_mag = sqrt(E1^2 + E2^2))
fi2_b <- sp2 +
  # Black outline segments (slightly larger size for the outline effect)
  geom_segment(data = dd1,
               aes(x = E1_ref, y = E2_ref,
                   xend = E1_ref + E1, yend = E2_ref + E2),
               color = "black", alpha = 1, size = 3.5) +  # Thicker black line for outline
  geom_segment(data = dd1,
               aes(x = E1_ref, y = E2_ref,
                   xend = E1_ref + E1, yend = E2_ref + E2,
                   col = dir_deriv), alpha = 1,
               size=2.5) +
  scale_colour_gradient2(low = muted("blue"),
                         mid = "white",
                         high = muted("red"),
                         midpoint = 0) +
  theme(
    axis.text = element_text(color = "black"),  # Set axis text to black
    axis.ticks = element_line(color = "black")  # Set axis ticks to black
  ) 


## Fig 2 a - one point

fig2_b <- fi2_b + 
  labs(tag = "(b)") + 
  guides(fill = guide_colourbar(title = "Growth \n Rate")) +
  theme(
    legend.key.width = unit(0.28, "cm"),  # Adjust width of the legend key
    legend.key.height = unit(0.28, "cm")
  ) + 
  labs(color = "Directional \n  Derivative", title = "Monoraphidium", x = "Temperature (°C)", y = "Phosphate concentration (μmol L−1)") +
  theme_classic(base_size = 30) +
  theme(
    plot.title = element_text(hjust = 0.5, vjust = 1, face = "bold")  # Center and adjust the title
  )

## Fig 2 d - grid of points
p2.2 <-draw(m_Monoraphidium, rug = FALSE, dist = 0.5, n_contour = 10,
            contour_col = "black") &
  labs(title = "Monoraphidium", x = "Temperature (°C)", y = "Phosphate concentration (μmol L−1)", tag = "(a)", caption = "") &
  ylim(4, 30) & xlim(15, 40) &
  scale_fill_gradient(low = "white", 
                      high = "darkgrey") & 
  labs(title = "", fill = "Growth", caption = "") & theme_classic(base_size = 20) 


refs <- crossing(E1 = seq(15, 40, 2),
                 E2 = seq(5, 30, 2))

refs_2 <- get_partials(m_Monoraphidium, refs) %>% 
  dplyr::mutate(E1_ref = E1, E2_ref = E2) 

radius <- .8
num_arrows <- 20
dd1 <- tibble(angle = rep(seq(0, 2*pi, length = num_arrows), nrow(refs_2))) %>%
  mutate(E1_ref = rep(refs_2$E1_ref, each = num_arrows),
         E2_ref = rep(refs_2$E2_ref, each = num_arrows)) %>%
  full_join(refs_2) %>%
  mutate(E1 = cos(angle) * radius,
         E2 = sin(angle) * radius,
         dir_deriv = E1 * pd_E1 + E2 * pd_E2,
         unit_vec_mag = sqrt(E1^2 + E2^2))

fig2_d <- p2.2 +
  # Black outline segments (slightly larger size for the outline effect)
  geom_segment(data = dd1,
               aes(x = E1_ref, y = E2_ref,
                   xend = E1_ref + E1, yend = E2_ref + E2),
               color = "black", alpha = 0.7, size = 2) +  # Thicker black line for outline
  geom_segment(data = dd1,
               aes(x = E1_ref, y = E2_ref,
                   xend = E1_ref + E1, yend = E2_ref + E2,
                   col = dir_deriv), alpha = 0.7,
               size=1) +
  scale_colour_gradient2(low = muted("blue"),
                         mid = "white",
                         high = muted("red"),
                         midpoint = 0) + 
  labs(x = "Temperature (°C)", y = "Phosphate concentration (μmol L−1)", tag = "(d)")+
  ylim(4, 30) + xlim(15, 40) +
  theme_classic(base_size = 30) +
  theme(
    axis.text = element_text(color = "black"),  # Set axis text to black
    axis.ticks = element_line(color = "black")  # Set axis ticks to black
  )


### Fig 2f
refs <- crossing(E1 = seq(18, 40, 8),
                 E2 = seq(7, 30, 10))

refs_2 <- get_partials(m_Monoraphidium, refs) %>% 
  dplyr::mutate(E1_ref = E1, E2_ref = E2) 


radius <- 2.5
num_arrows <- 4
dd1 <- tibble(angle = rep(seq(0, 2*pi, length = num_arrows), nrow(refs_2))) %>%
  mutate(E1_ref = rep(refs_2$E1_ref, each = num_arrows),
         E2_ref = rep(refs_2$E2_ref, each = num_arrows)) %>%
  full_join(refs_2) %>%
  mutate(E1 = cos(angle) * radius,
         E2 = sin(angle) * radius,
         dir_deriv = E1 * pd_E1 + E2 * pd_E2,
         unit_vec_mag = sqrt(E1^2 + E2^2))

fig2_f<- p2.2 +
  # Black outline segments (slightly larger size for the outline effect)
  geom_segment(data = dd1,
               aes(x = E1_ref, y = E2_ref,
                   xend = E1_ref + E1, yend = E2_ref + E2),
               color = "black", alpha = 1, size = 4.5) +  # Thicker black line for outline
  geom_segment(data = dd1,
               aes(x = E1_ref, y = E2_ref,
                   xend = E1_ref + E1, yend = E2_ref + E2,
                   col = dir_deriv), alpha = 1,
               size=3.5) +
  scale_colour_gradient2(low = muted("blue"),
                         mid = "white",
                         high = muted("red"),
                         midpoint = 0) + 
  geom_point(data = dd1,
             aes(x = E1_ref, y = E2_ref,
                 size = 3))+
  labs(x = "Temperature (°C)", y = "Phosphate concentration (μmol L−1)", tag = "(f)")+
  ylim(4, 30) + xlim(15, 40) +
  theme_classic(base_size = 30) +
  theme(
    axis.text = element_text(color = "black"),  # Set axis text to black
    axis.ticks = element_line(color = "black")  # Set axis ticks to black
  )


# 1. Adding common x-axis and y-axis labels, and adjusting axis text
common_x <- "Temperature (°C)"
common_y <- "Phosphate \nconcentration (μmol L−1)"

# Adjust the axis labels and sizes
common_theme <- theme(
  axis.text = element_text(color = "black", size = 30),   # Increase axis text size
  axis.title = element_text(size = 35, face = "bold"),    # Increase axis title size
  axis.ticks = element_line(color = "black"),
  plot.margin = margin(5, 5, 5, 5)                       # Adjust margin around each panel
)

# 2. Hiding specific axis elements for cleaner layout
# Only display y-axis for panels a, c, e
# Only display x-axis for panels e, f

fig2_a <- fig2_a +
  labs(x = NULL, y = common_y) +  # Keep y-axis label, hide x-axis label
  theme(axis.text.x = element_blank()) +
  common_theme

fig2_b <- fig2_b +
  labs(x = NULL, y = NULL) +       # Hide both x and y axis labels
  theme(axis.text.x = element_blank(),
        axis.text.y = element_blank()) +
  common_theme

fig2_c <- fig2_c +
  labs(x = NULL, y = common_y) +  # Keep y-axis label, hide x-axis label
  theme(axis.text.x = element_blank()) +
  common_theme + labs(title = NULL)

fig2_d <- fig2_d +
  labs(x = NULL, y = NULL) +       # Hide both x and y axis labels
  theme(axis.text.x = element_blank(),
        axis.text.y = element_blank()) +
  common_theme + labs(title = NULL)

fig2_e <- fig2_e +
  labs(x = common_x, y = common_y) +  # Keep both x and y axis labels
  common_theme + labs(title = NULL)

fig2_f <- fig2_f +
  labs(x = common_x, y = NULL) +  # Keep x-axis label, hide y-axis label
  theme(axis.text.y = element_blank()) +
  common_theme + labs(title = "")

# 3. Combine the plots into a layout, and adjust axis visibility and spacing
combined_plots <- wrap_plots(
  fig2_a + theme(legend.position = "none"),
  fig2_b + theme(legend.position = "none"),
  fig2_c + theme(legend.position = "none"),
  fig2_d + theme(legend.position = "none"),
  fig2_e + theme(legend.position = "none"),
  fig2_f + theme(legend.position = "none"),
  nrow = 3
)

# 4. Extract the legend from fig2_b
legend <- get_legend(
  fig2_b + theme(
    legend.title = element_text(size = 30),  # Increase legend title size
    legend.text = element_text(size = 28),   # Increase legend text size
    legend.key.size = unit(2.5, "lines")     # Increase legend key size
  )
)

# 5. Final layout with combined plots and legend
final_plot <- plot_grid(
  combined_plots,
  legend,
  ncol = 2,
  rel_widths = c(3, 0.5),  # Adjust space between plots and legend
  align = 'v'
) +
  theme(plot.margin = margin(0, 0, 0, 0))  # Adjust margins to reduce white space

# 6. Display the final plot
print(final_plot)




# new
# 3. Combine the plots into a layout, and adjust axis visibility and spacing
combined_plots <- wrap_plots(
  fig2_a + theme(legend.position = "none"),
  fig2_b + theme(legend.position = "none"),
  fig2_c + theme(legend.position = "none"),
  fig2_d + theme(legend.position = "none"),
  fig2_e + theme(legend.position = "none"),
  fig2_f + theme(legend.position = "none"),
  nrow = 3
) +
  plot_layout(guides = "collect", heights = c(1, 1, 1))  # Reduce space between rows

# 5. Final layout with combined plots and legend
final_plot <- plot_grid(
  combined_plots,
  legend,
  ncol = 2,
  rel_widths = c(3, 0.5),  # Adjust space between plots and legend
  align = 'v'
) +
  theme(plot.margin = margin(0, 0, 0, 0))  # Adjust margins to reduce white space

# 6. Display the final plot
print(final_plot)

ggsave(final_plot, file = here("Revision/figure_revision/Fig2try.png"), width = 22, height = 22)


######## Figure 3 ############
# Fug 3 is create in the RMarkdown document called "Empirical_application" and loaded at the beginning of this script
load(here("Data/Empirical_example.RData"))
combined_plots

ggsave(combined_plots, file = here("Revision/figure_revision/Fig.3.png"), width = 20, height = 10)
ggsave(combined_plots, file = here("Revision/figure_revision/Fig.3.pdf"), width = 20, height = 10)
########## Figure 4 ##################
### first panel 
load(here("Data/my_work_space.RData"))
E1_series <- 273.15 + seq(0, 50, 1)
E2_series <- seq(0, 50, 1)


## Set parameter values - without interaction
pars <- list(a1 = 1e-9,
             b1 = 0.063,
             z1 = 285,
             w1 = 60,
             a2 = 1e-3,
             b2 = 0.02,
             z2 = 20,
             w2 = 10,
             z_int21 = 0,
             wint = 0,
             sd_rate = 0)
## Make a surface / experiment 
expt <- Make_expt(E1_series, E2_series, pars)
expt[[1]]$E1 <- expt[[1]]$E1 - 273.15

## Visualise the performance surface of a species
pc_figs <- Plot_performance_curves(expt[[1]])
fig1 <- pc_figs[[1]]+ theme_classic() + pc_figs[[2]] + theme_classic()
fig1 


pp1 <- fig1[[1]] + labs(x = "E1", y = "Growth Rate",  tag = "(a)") +
  theme_classic(base_size = 15) + 
  guides(color=guide_legend(title="E2")) +
  fig1[[2]] +
  theme_classic(base_size = 15) +
  labs(x = "E2", y = "Growth Rate", tag = "")+
  guides(color=guide_legend(title="E1"))

pp1 




E1_series <- seq(0, 50, 1)
E2_series <- seq(0, 50, 1)


## Set parameter values - without interaction
pars <- list(a1 = 1e-3,
             b1 = 0.02,
             z1 = 20,
             w1 = 10,
             a2 = 1e-3,
             b2 = 0.02,
             z2 = 20,
             w2 = 10,
             z_int21 = 0,
             wint = 0,
             sd_rate = 0)
## Make a surface / experiment 
expt <- Make_expt(E1_series, E2_series, pars)


## Visualise the performance surface of a species
pc_figs <- Plot_performance_curves(expt[[1]])
fig1 <- pc_figs[[1]]+ theme_classic() + pc_figs[[2]] + theme_classic()
fig1 



## Set parameter values - with interaction
pars <- list(a1 = 1e-3,
             b1 = 0.02,
             z1 = 20,
             w1 = 10,
             a2 = 1e-3,
             b2 = 0.02,
             z2 = 20,
             w2 = 10,
             z_int21 = 0.2,
             wint = 0,
             sd_rate = 0)
## Make a surface / experiment 
expt <- Make_expt(E1_series, E2_series, pars)


## Visualise the performance surface of a species
pc_figs <- Plot_performance_curves(expt[[1]])
fig2 <- pc_figs[[1]]+ theme_classic() + pc_figs[[2]] + theme_classic()

fig1 + theme_classic() + 
  theme(plot.title = element_text(hjust = 0.5), 
        plot.title.position = "plot")
pp2 <- fig1[[1]] + labs(x = "E1", y = "Growth Rate") +
  theme_classic(base_size = 15) +  
  guides(color=guide_legend(title="E2")) +
  fig1[[2]]  +
  theme_classic(base_size = 15) +
  labs(x = "E2", y = "Growth Rate", tag = "")+
  guides(color=guide_legend(title="E1"))




pp1 <- pp1 + ggtitle("E1 dominant environmental variable") +
  theme(plot.title = element_text(hjust = 3))

pp2 <- pp2 + ggtitle("Equal effect of E1 and E2") +
  theme(plot.title = element_text(hjust = 100))

simulation_curves <- pp1/pp2 
simulation_curves
# create data frame
library(truncnorm)

set.seed(46354)
df_low_z1 <- data.frame(E1 = c(rtruncnorm(n=1000, a=0, b=50, mean=25, sd=3), rtruncnorm(n=1000, a=0, b=50, mean=24, sd=3), rtruncnorm(n=1000, a=0, b=50, mean=26, sd=3),
                               rtruncnorm(n=1000, a=0, b=50, mean=28, sd=3)),
                        species = rep(c("Sp 1", "Sp 2", "Sp 3", "Sp 4"), each = 1000))

# plot normal distributions
low_z1_div <- ggplot(df_low_z1, aes(x = E1, fill = species)) +
  annotate('rect', xmin=0, xmax=15, ymin=0.0, ymax=0.2, alpha=.2, fill='red')+
  annotate('text', x=8, y=0.205, label='Low mean E1')+
  annotate('rect', xmin=15, xmax=35, ymin=0.0, ymax=0.2, alpha=.2, fill='green')+
  annotate('text', x=23.5, y=0.205, label='Intermediate mean E1')+
  annotate('rect', xmin=35, xmax=50, ymin=0.0, ymax=0.2, alpha=.2, fill='blue')+
  annotate('text', x=42, y=0.205, label='High mean E1')+
  geom_density(alpha = 0.8) +
  scale_fill_viridis_d(option = "magma") +
  theme_classic() +
  labs(tag = "(b)", y = "Growth rate") + ggtitle("Low interspecific diversity in E1 optima") +labs(x = "E1") +
  theme(
    plot.title = element_text(hjust = 0.5)) +
  theme_classic(base_size = 15) +
  theme(
    plot.title = element_text(hjust = 0.5)) +
  xlim(0, 50) +
  theme(
    plot.title = element_text(hjust = 0.5)) + 
  theme(legend.position = "none")



# create data frame
set.seed(46354)
df_medium_z1 <- data.frame(E1 = c(rtruncnorm(n=1000, a=0, b=50, mean=15, sd=3), rtruncnorm(n=1000, a=0, b=50, mean=24, sd=3), rtruncnorm(n=1000, a=0, b=50, mean=30, sd=3),
                                  rtruncnorm(n=1000, a=0, b=50, mean=35, sd=3)),
                           species = rep(c("Sp 1", "Sp 2", "Sp 3", "Sp 4"), each = 1000))
# plot normal distributions
medium_z1_div <- ggplot(df_medium_z1, aes(x = E1, fill = species)) +
  annotate('rect', xmin=0, xmax=15, ymin=0.0, ymax=0.2, alpha=.2, fill='red')+
  annotate('text', x=8, y=0.205, label='Low mean E1')+
  annotate('rect', xmin=15, xmax=35, ymin=0.0, ymax=0.2, alpha=.2, fill='green')+
  annotate('text', x=23.5, y=0.205, label='Intermediate mean E1')+
  annotate('rect', xmin=35, xmax=50, ymin=0.0, ymax=0.2, alpha=.2, fill='blue')+
  annotate('text', x=42, y=0.205, label='High mean E1')+
  geom_density(alpha = 0.8) +
  scale_fill_viridis_d(option = "magma") +
  theme_classic() +
  labs(y = "Growth rate") + ggtitle("Medium interspecific diversity in E1 optima") +labs(x = "E1") +
  theme(
    plot.title = element_text(hjust = 0.5))+
  theme_classic(base_size = 15) +
  theme(
    plot.title = element_text(hjust = 0.5)) +
  xlim(0, 50) +
  theme(
    plot.title = element_text(hjust = 0.5)) + 
  theme(legend.position = "none")


medium_z1_div

# create data frame
set.seed(46354)
df_high_z1 <- data.frame(E1 = c(rtruncnorm(n=1000, a=0, b=50, mean=5, sd=4), rtruncnorm(n=1000, a=0, b=50, mean=18, sd=3), rtruncnorm(n=1000, a=0, b=50, mean=26, sd=3),
                                rtruncnorm(n=1000, a=0, b=50, mean=45, sd=3)),
                         species = rep(c("Sp 1", "Sp 2", "Sp 3", "Sp 4"), each = 1000))

# plot normal distributions
high_z1_div <- ggplot(df_high_z1, aes(x = E1, fill = species)) +
  annotate('rect', xmin=0, xmax=15, ymin=0.0, ymax=0.2, alpha=.2, fill='red')+
  annotate('text', x=8, y=0.205, label='Low mean E1')+
  annotate('rect', xmin=15, xmax=35, ymin=0.0, ymax=0.2, alpha=.2, fill='green')+
  annotate('text', x=23.5, y=0.205, label='Intermediate mean E1')+
  annotate('rect', xmin=35, xmax=50, ymin=0.0, ymax=0.2, alpha=.2, fill='blue')+
  annotate('text', x=42, y=0.205, label='High mean E1')+
  geom_density(alpha = 0.8) +
  theme_classic(base_size = 15) +
  scale_fill_viridis_d(option = "magma") +
  theme_classic(base_size = 15) +
  labs(y = "Growth rate") + ggtitle("High interspecific diversity in E1 optima") +labs(x = "E1") +
  theme(
    plot.title = element_text(hjust = 0.5))+
  xlim(0, 50) +
  theme(
    plot.title = element_text(hjust = 0.5))

high_z1_div




comb <- low_z1_div / medium_z1_div / high_z1_div +
  theme(legend.position = "bottom")  # Set legend position for each plot

fig_optimum <- comb + 
  plot_layout( heights = c(0.5, 0.5, 0.6))
# Position of the optimum


scenario1 <- optima_scenario1 %>%  ggplot(aes(x = E1, y = E2, col = as.factor(community))) +
  geom_point(size = 8) +
  scale_colour_viridis_d()+
  labs(tag = "(c)", x = "Optimum E1", y = "Optimum E2",  col = ("community"), title = "Communities vary in only the amount of\n interspecific diversity in E1 optima \n - No correlation") +
  theme_classic(base_size = 15) +
  ylim(0, 50) +
  theme(axis.text = element_blank(),  # Remove axis labels
        axis.ticks = element_blank())


scenario2 <- optima_scenatio2 %>%  ggplot(aes(x = E1, y = E2, col = as.factor(community))) +
  geom_point(size = 8) +
  scale_colour_viridis_d()+
  labs(x = "Optimum E1", y = "Optimum E2", col = ("community"), title = "Communities vary in both the amount of\n interspecific diversity in E1 and E2 optima \n - Positive correlation") +
  theme_classic(base_size = 15) +
  ylim(0, 50) +
  theme(axis.text = element_blank(),  # Remove axis labels
        axis.ticks = element_blank())

scenario3 <- optima_scenario3 %>%  ggplot(aes(x = E1, y = E2, col = as.factor(community))) +
  geom_point(size = 8) +
  scale_colour_viridis_d()+
  labs(x = "Optimum E1", y = "Optimum E2", col = ("community"), title = "Communities vary in both the amount of\n interspecific diversity in E1 and E2 optima \n - Negative correlation") +
  theme_classic(base_size = 15) +
  ylim(0, 50) +
  theme(axis.text = element_blank(),  # Remove axis labels
        axis.ticks = element_blank())




optimum_scenarios <- scenario1 / scenario2 / scenario3 & theme(legend.position = "bottom")
optimum_scenarios <- optimum_scenarios + patchwork::plot_layout(guides = "collect")
optimum_scenarios



pp1 <- pp1 + ggtitle("E1 dominant environmental variable") +
  theme(plot.title = element_text(hjust = 2))

pp2 <- pp2 + ggtitle("Equal effect of E1 and E2") +
  theme(plot.title = element_text(hjust = 4.5))

simulation_curves <- pp1/pp2 
simulation_curves

left_panel <- (simulation_curves + fig_optimum) + plot_layout(heights = c(0.1, 0.1, 0.6))

# fig_4 <- left_panel + optimum_scenarios + plot_layout(ncol = 2)


fig_4 <- ggpubr::ggarrange(left_panel, optimum_scenarios, widths = c(3,2), 
                           ncol = 2)  
fig_4
# fig_Optimum_complete <- ggpubr::ggarrange(fig_optimum, optimum_scenarios, final_plot_corr, heights = c(2,2,2), 
#                                           ncol = 3)  
# fig_Optimum_complete 


ggsave(fig_4, file = here("Revision/figure_revision/Fig4.jpg"), width = 15, height = 18)
ggsave(fig_4, file = here("Revision/figure_revision/Fig4.png"), width = 15, height = 18)
ggsave(fig_4, file = here("Revision/figure_revision/Fig4.pdf"), width = 15, height = 18)




############ Figure 5 ################
load(here("Data/my_work_space.RData"))
# Figure 5
theme_set(theme_classic(base_size = 30))
div1 <-  ggplot(dd_divergence_scenario1, aes(x = z1, y = sign, color = E1_mean)) +
  # scale_y_continuous(limits = c(0.99, 1.005), expand = c(0.02, 0.02)) +
  scale_color_uchicago() +
  labs( y = "Divergence") +
  theme(
    legend.position = "none",
    axis.title.x = element_blank(),
    axis.title = element_text(size = 30),
    panel.grid = element_blank()
  ) +
  geom_jitter(size = 2, alpha = 0.40, width = 0.2) +
  stat_summary(fun = mean, geom = "point", size = 7) + labs(tag = "(a)")+ 
  scale_x_discrete(limits = level_order)  + ggtitle("Only variation in E1\n optimum diversity") +
  theme(
    axis.text = element_text(color = "black"),  # Set axis text to black
    axis.ticks = element_line(color = "black")  # Set axis ticks to black
  )


div2 <-  ggplot(dd_divergence_scenario2, aes(x = z1, y = sign, color = E1_mean)) +
  # scale_y_continuous(limits = c(0.99, 1.005), expand = c(0.02, 0.02)) +
  scale_color_uchicago() +
  labs(x = "E1 optimum diversity", y = "Divergence") +
  theme(
    legend.position = "none",
    axis.title = element_blank(),
    axis.text = element_blank(),
    axis.text.y = element_blank(),
    panel.grid = element_blank()
  ) +
  geom_jitter(size = 2, alpha = 0.40, width = 0.2) +
  stat_summary(fun = mean, geom = "point", size = 7) + labs(tag = "(b)")+ 
  scale_x_discrete(limits = level_order) + ggtitle("Variation in E1 and E2\noptimum diversity - \npositive correlation") +
  theme(
    axis.text = element_text(color = "black"),  # Set axis text to black
    axis.ticks = element_line(color = "black")  # Set axis ticks to black
  )

div3 <-  ggplot(dd_divergence_scenario3, aes(x = z1, y = sign, color = E1_mean)) +
  # scale_y_continuous(limits = c(0.99, 1.005), expand = c(0.02, 0.02)) +
  scale_color_uchicago() +
  labs(x = "E1 optimum diversity", y = "Divergence") +
  theme(
    legend.position = "none",
    axis.title = element_blank(),
    axis.text = element_blank(),
    axis.text.y = element_blank(),
    panel.grid = element_blank()
  ) +
  geom_jitter(size = 2, alpha = 0.40, width = 0.2) +
  stat_summary(fun = mean, geom = "point", size = 7) + labs(tag = "(c)")+ 
  scale_x_discrete(limits = level_order) + ggtitle("Variation in E1 and E2\noptimum diversity - \nnegative correlation") +
  theme(
    axis.text = element_text(color = "black"),  # Set axis text to black
    axis.ticks = element_line(color = "black")  # Set axis ticks to black
  )


div4 <-  ggplot(dd_divergence_scenario1_SAME, aes(x = z1, y = sign, color = E1_mean)) +
  # scale_y_continuous(limits = c(0.99, 1.005), expand = c(0.02, 0.02)) +
  scale_color_uchicago() +
  labs(x = "E1 optimum diversity", y = "Divergence") +
  theme(
    legend.position = "none",
    axis.title.x  = element_blank(),
    axis.title = element_text(size = 30),
    panel.grid = element_blank()
  ) +
  geom_jitter(size = 2, alpha = 0.40, width = 0.2) +
  stat_summary(fun = mean, geom = "point", size = 7) + labs(tag = "(d)")+ 
  scale_x_discrete(limits = level_order)   + ggtitle("Only variation in E1\n optimum diversity") +
  theme(
    axis.text = element_text(color = "black"),  # Set axis text to black
    axis.ticks = element_line(color = "black")  # Set axis ticks to black
  )


div5 <-  ggplot(dd_divergence_scenario2_SAME, aes(x = z1, y = sign, color = E1_mean)) +
  # scale_y_continuous(limits = c(0.99, 1.005), expand = c(0.02, 0.02)) +
  scale_color_uchicago() +
  labs(x = "E1 optimum diversity", y = "Divergence") +
  theme(
    legend.position = "none",
    axis.text = element_blank(),
    axis.title.y  = element_blank(),
    axis.text.y = element_blank(),
    panel.grid = element_blank()
  ) +
  geom_jitter(size = 2, alpha = 0.40, width = 0.2) +
  stat_summary(fun = mean, geom = "point", size = 7) + labs(tag = "(e)")+ 
  scale_x_discrete(limits = level_order) + ggtitle("Variation in E1 and E2\noptimum diversity - \npositive correlation") +
  theme(
    axis.text = element_text(color = "black"),  # Set axis text to black
    axis.ticks = element_line(color = "black")  # Set axis ticks to black
  )


div6 <-  ggplot(dd_divergence_scenario3_SAME, aes(x = z1, y = sign, color = E1_mean)) +
  # scale_y_continuous(limits = c(0.99, 1.005), expand = c(0.02, 0.02)) +
  scale_color_uchicago() +
  labs(x = "E1 optimum diversity", y = "Divergence") +
  theme(
    legend.position = "none",
    axis.title.x  = element_blank(),
    axis.title.y  = element_blank(),
    axis.text = element_blank(),
    axis.text.y = element_blank(),
    panel.grid = element_blank()
  ) +
  geom_jitter(size = 2, alpha = 0.40, width = 0.2) +
  stat_summary(fun = mean, geom = "point", size = 7) + labs(tag = "(f)")+ 
  scale_x_discrete(limits = level_order) + ggtitle("Variation in E1 and E2\noptimum diversity-\nnegative correlation") +
  theme(
    axis.text = element_text(color = "black"),  # Set axis text to black
    axis.ticks = element_line(color = "black")  # Set axis ticks to black
  )





top_plot      <- wrap_elements((div1 + div2 + div3 & theme(legend.position = "bottom") & 
                                  labs(col = "E1 mean value")) + plot_layout(guides = "collect")+
                                 plot_annotation(title = "E1 dominant environmental variable",
                                                 theme = theme(plot.title = element_text(hjust = 0.5))))


bottom_plot   <- wrap_elements((div4 + div5 + div6 & theme(legend.position = "bottom") & 
                                  labs(col = "E1 mean value")) + plot_layout(guides = "collect")+ 
                                 plot_annotation(title = "Equal effect of E1 and E2",
                                                 theme = theme(plot.title = element_text(hjust = 0.5))))


combined_plot <- top_plot / bottom_plot + 
  plot_layout(guides = "collect")




ggsave(combined_plot, file = here("Revision/figure_revision/Fig5.jpg"), width = 30, height = 25)
ggsave(combined_plot, file = here("Revision/figure_revision/Fig5.png"), width = 30, height = 25)
ggsave(combined_plot, file = here("Revision/figure_revision/Fig5.pdf"), width = 30, height = 25)


