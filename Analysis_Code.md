# ACUPCC #


```
## data.csv = Copy of R_CLEAN_acupcc-DATA_CHECK-1.xlsx downloaded from issue #?
## (e.g. http://unm-macroecology-2012.googlecode.com/files/ACUPCC-clean-finished-trim.csv )

data <- read.table("data.csv", header = TRUE, sep = ",")

##GDP v. emissions (x) 
##need to multiply no. of students (in & out) by tuition
##Population v. emissions (x)
##Area v. emissions (x)
##Density v. emissions (x)
##Temp v.
##Precip v. 
##Seasonality (sd) v.

require(lattice)
require(plyr)
require(smtr)

data2 <- subset(data, year >= 2005, select = c(name, Gross_emissions_.Scopes_1_._2., 
    Gross_emissions_.Scopes_1_._2_._3., Net_emissions, Purchased_Electricity, 
    Commuting, Gross_square_feet_of_building_space, Total_Student_Enrollment_.FTE.,
    Full.time_Faculty, Part.time_Faculty, Full.time_Staff, Part.time_Staff, Endowment_Size))

## calculate per-institution medians of relevant columns (e.g. marginalize wrt time)
data_bin <- ddply(data2,"name",function(x)
  c(Gross_emiss_1_2 = median(x$Gross_emissions_.Scopes_1_._2.), 
    Gross_emiss_1_2_3 = median(x$Gross_emissions_.Scopes_1_._2_._3.), 
    Net_emiss = median(x$Net_emissions), 
    Purch_Elec = median(x$Purchased_Electricity), 
    Commute = median(x$Commuting), 
    Sq_Ft = median(x$Gross_square_feet_of_building_space), 
    Enroll = median(x$Total_Student_Enrollment_.FTE.), 
    Faculty = median(sum(x$Full.time_Faculty, x$Part.time_Faculty)), 
    Staff = median(sum(x$Full.time_Staff, x$Part.time_Staff)), 
    Endow = median(x$Endowment_Size), n = nrow(x)
  )
)

## Vector of per-campus total populations
pop <- c()
for(i in 1:length(data_bin$name)){
  pop[i] <- sum(data_bin$Staff[i] + data_bin$Enroll[i] + data_bin$Faculty[i])
}

density <- pop/data_bin$Sq_Ft

## emissions per person
Unit_C <- data_bin$Gross_emiss_1_2/pop

##plot
plot(log10(pop), log10(data_bin$Gross_emiss_1_2), xlab = "Log(Population)", ylab = "Log(Gross Emissions)")
sma(log10(data_bin$Gross_emiss_1_2) ~ log10(pop), method = "MA")
sma(log10(data_bin$Gross_emiss_1_2) ~ log10(pop), method = "OLS")
## could not figure out how to get coefficients from objects
abline(a = -0.5749720, b = 1.207776) #RMA
abline(a = 0.8456880, b = 0.8380508, lty = 2) #OLS
#R^2 = 0.53
## ?? use sprintf() function to put data in a string, e.g.
## aa = 0.53; sprintf("R^2 = %s", aa) 
## what is sprintf()?
legend(3, 7, legend = c("R^2 = 0.53", "OLS y = 0.85 + 0.84x", "RMA y = -0.57 + 1.2x"))

plot(log10(pop), log10(data_bin$Net_emiss), xlab = "Log(Population)", ylab = "Log(Net Emissions)")
sma(log10(data_bin$Net_emiss) ~ log10(pop), method = "MA")
sma(log10(data_bin$Net_emiss) ~ log10(pop), method = "OLS")
abline(a = -0.01473721, b = 1.1188729)
abline(a = 0.7679336, b = 0.9153626, lty = 2)
#R^2 = 0.69
legend(3, 7, legend = c("R^2 = 0.69", "OLS y = 0.76 + 0.9x", "RMA y = -0.01 + 1.1x"))

plot(log10(data_bin$Sq_Ft), log10(data_bin$Gross_emiss_1_2), xlab = "Log(Sq Ft)", ylab = "Log(Gross Emissions)")
sma(log10(data_bin$Gross_emiss_1_2)[-424] ~ log10(data_bin$Sq_Ft)[-424], method = "MA")
sma(log10(data_bin$Gross_emiss_1_2)[-424] ~ log10(data_bin$Sq_Ft)[-424], method = "OLS")
abline(a = -3.679073, b = 1.283896)
abline(a = -2.515231, b = 1.092456, lty = 2)
#R^2 = 0.8
legend(4, 6, legend = c("R^2 = 0.8", "OLS y = -2.5 + 1.1x", "RMA y = -3.7 + 1.3x"))

plot(log10(data_bin$Endow), log10(data_bin$Gross_emiss_1_2), xlab = "Log(Endowment)", ylab = "Log(Gross Emissions)")
sma(log10(data_bin$Gross_emiss_1_2) ~ log10(data_bin$Endow), method = "MA")
sma(log10(data_bin$Gross_emiss_1_2) ~ log10(data_bin$Endow), method = "OLS")
abline(a = 1.2979265, b = 0.3917707)
abline(a = 2.088001, b = 0.2849266, lty = 2)
#R^2 = 0.21
legend(3, 7, legend = c("R^2 = 0.21", "OLS y = 2 + 0.28x", "RMA y = 1.3 + 0.39x"))

plot(log10(density), log10(Unit_C), ylab = "Log(Emissions/Person)", xlab = "Log(Density)")
sma(log10(Unit_C) ~ log10(density), method = "MA")
sma(log10(Unit_C) ~ log10(density), method = "OLS")
abline(a = -2.091742, b = -1.0662511)
abline(a = -1.635067, b = -0.8559271, lty = 2)
#R^2 = 0.66
legend(-3, -0.5, legend = c("R^2 = 0.66", "OLS y = -1.6 - 0.86x", "RMA y = -2.1 - 1.1x"))
```

Analysis code (not including Natalie's code that added in heating and cooling days):
```
data <- read.table("FIN.Alldata.csv", header = TRUE, sep = ",")

require(lattice)
require(plyr)
require(smatr)

data2 <- subset(data, year.y >= 2005, select = c(college, Gross_emissions_.Scopes_1_._2., Gross_emissions_.Scopes_1_._2_._3., Net_emissions, Purchased_Electricity, Commuting, Gross_square_feet_of_building_space, Total_Student_Enrollment_.FTE., drvhr2010.tftestf.VL.Total.FTE.staff, drvef122010.undup.VL.12.month.unduplicated.headcount..total..2009.10, temp.F.mean, temp.F.sd, precip.sum.In, precip.days, rel.humid.mean, rel.humid.sd))

data_bin <- ddply(data2,"college",function(x)c(Gross_emiss_1_2 = median(x$Gross_emissions_.Scopes_1_._2.), Gross_emiss_1_2_3 = median(x$Gross_emissions_.Scopes_1_._2_._3.), Net_emiss = median(x$Net_emissions), Purch_Elec = median(x$Purchased_Electricity), Commute = median(x$Commuting), Sq_Ft = median(x$Gross_square_feet_of_building_space), Enroll = median(x$Total_Student_Enrollment_.FTE.), Fac_Staff = median(x$drvhr2010.tftestf.VL.Total.FTE.staff), pop = median(x$drvef122010.undup.VL.12.month.unduplicated.headcount..total..2009.10), temp_mean = median(x$temp.F.mean), temp_sd = median(x$temp.F.sd), precip_sum = median(x$precip.sum.In), precip_days = median(x$precip.days), humid_mean = median(x$rel.humid.mean), humid_sd = median(x$rel.humid.sd), n = nrow(x)))

data3 <- subset(data, year.y >= 2005, select = c(college, hd2010.locale.VL.Degree.of.urbanization..Urban.centric.locale., hd2010.ccsizset.VL.Carnegie.Classification.2010..Size.and.Setting, hd2010.control.VL.Control.of.institution, hd2010.hloffer.VL.Highest.level.of.offering, hd2010.stabbr.VL.State.abbreviation))

data4 <- subset(data3, !duplicated(college))
colnames(data4)[2] <- "urban"
colnames(data4)[3] <- "size"
colnames(data4)[4] <- "institution"
colnames(data4)[5] <- "degree"
colnames(data4)[6] <- "state"

density <- data_bin$pop/data_bin$Sq_Ft

Unit_C <- data_bin$Gross_emiss_1_2_3/data_bin$pop

data5 <- cbind(data_bin, data4, density, Unit_C)
data5 <- as.data.frame(data5)

#delete the two crazy outliers
data <- subset(data5, Gross_emiss_1_2_3!=25160678)
data <- subset(data, Gross_emiss_1_2_3!=5103644)

#write.csv(data, file = "Macro_data.csv")

anova(lm(data$Gross_emiss_1_2_3~data$pop+data$density))
anova(lm(data$Gross_emiss_1_2_3~data$pop+data$Sq_Ft))

data1 <- subset(data, pop!='NA')

resid <- resid(lm(data1$Gross_emiss_1_2_3~data1$pop))
plot(resid~data1$density)

anova(lm(log10(data$Gross_emiss_1_2_3)~log10(data$pop)*log10(data$Sq_Ft)))
anova(lm(data$Gross_emiss_1_2_3~data$pop*data$Sq_Ft))
anova(lm(data$Gross_emiss_1_2_3~data$pop*data$Sq_Ft*density))
anova(lm(data$Gross_emiss_1_2_3~data$pop+data$Sq_Ft+density))
anova(lm(data$Gross_emiss_1_2_3~density))
anova(lm(data$pop~data$Sq_Ft))

plot(log10(data$Gross_emiss_1_2_3)~log10(data$Sq_Ft),
     xlab = 'College building area (square feet)',
     ylab = 'Gross emissions (metric tons CO2)',
     main = 'Gross emissions of US colleges by area',
     pch=21, bg = 'red',
     xaxt = "n", yaxt = "n",
     cex.lab = 1.8, cex = 1.5, cex.main = 2)
axis(1, at = seq(3.5, 7.5, .5), labels = c("10^3.5", "10^4", "10^4.5", "10^5", "10^5.5", "10^6", "10^6.5", "10^7", "10^7.5"))
axis(2, at = seq(2.5, 6.5, .5), labels = c("10^2.5", "10^3", "10^3.5", "10^4", "10^4.5", "10^5", "10^5.5", "10^6", "10^6.5"))
sma(log10(data$Gross_emiss_1_2_3) ~ log10(data$Sq_Ft), method = "OLS")
abline(a = -1.410341, b = 0.9456158, lwd = 1.25) #R^2 = 0.7698904; p-value = < 2.22e-16
legend(4, 6, legend = c("y = 0.95x - 1.41", "R^2 = 0.77", "p-value < 0.0001"),
       x.intersp = 0.5, y.intersp = 0.5)

summary(lm(log10(data$Gross_emiss_1_2_3)~log10(data$Sq_Ft)+log10(data$pop)+data$temp_sd))
summary(lm(log10(data$Gross_emiss_1_2_3)~log10(data$Sq_Ft)+log10(data$pop)+data$temp_mean+data$temp_sd))
summary(lm(log10(data$Gross_emiss_1_2_3)~log10(data$Sq_Ft)+log10(data$pop)+data$temp_mean+data$temp_sd+data$precip_sum)) #precip not sig
summary(lm(log10(data$Gross_emiss_1_2_3)~log10(data$Sq_Ft)+log10(data$pop)+data$temp_mean+data$temp_sd+data$precip_days)) #precip days not sig
summary(lm(log10(data$Gross_emiss_1_2_3)~log10(data$Sq_Ft)+log10(data$pop)+data$temp_mean+data$temp_sd+data$humid_mean)) #humid mean not sig
summary(lm(log10(data$Gross_emiss_1_2_3)~log10(data$Sq_Ft)+log10(data$pop)+data$temp_mean+data$temp_sd+data$humid_sd)) #humid sd not sig
summary(lm(log10(data$Gross_emiss_1_2_3)~log10(data$Sq_Ft)+log10(data$pop)+data$temp_mean+data$temp_sd+data$urban)) #urban classification not sig - just "distant" is sig; positively correlated with emissions
summary(lm(log10(data$Gross_emiss_1_2_3)~log10(data$Sq_Ft)+log10(data$pop)+data$temp_mean+data$temp_sd+data$size)) #size not sig
summary(lm(log10(data$Gross_emiss_1_2_3)~log10(data$Sq_Ft)+log10(data$pop)+data$temp_mean+data$temp_sd+data$institution)) #institution not sig
summary(lm(log10(data$Gross_emiss_1_2_3)~log10(data$Sq_Ft)+log10(data$pop)+data$temp_mean+data$temp_sd+data$degree)) 
summary(lm(log10(data$Gross_emiss_1_2_3)~log10(data$Sq_Ft)+log10(data$pop)+data$temp_mean+data$temp_sd+data$state))

residuals <- resid(lm(log10(data1$Gross_emiss_1_2_3)~log10(data1$Sq_Ft)+log10(data1$pop)))
plot(residuals~data1$temp_mean)
summary(lm(residuals~data1$temp_sd)) #once pop and area are accounted for, temp sd only accounts for 3.7% of variation
summary(lm(residuals~data1$temp_mean))

plot(residuals~data1$temp_sd)

density <- data1$pop/data1$Sq_Ft

#comparison (table) rankings 1-20, rankings per capita 1-20, rankings based on residual values 1-20
residuals <- resid(lm(log10(data1$Gross_emiss_1_2_3)~log10(data1$Sq_Ft)+log10(data1$pop)))
data_resid <- cbind(data1, residuals)
data_resid <- as.data.frame(data_resid)
#write.csv(data_resid, file = "residual_data.csv")

#write.csv(data1, file = "rank_data.csv")

Unit_C <- data1$Gross_emiss_1_2_3/data1$pop

percap_rank <- Unit_C
data_percap_rank <- cbind(data1, percap_rank)
write.csv(data_percap_rank, file = "percap_data.csv")

#PCO
#subset of data that only includes variables that you want in the PCA; can't be any missing variables (drop all rows that have missing variables)
pca_data <- subset(data, select = c(Gross_emiss_1_2_3, Sq_Ft, pop, temp_sd), pop != "NA")
x <- princomp(~ Gross_emiss_1_2_3 + Sq_Ft + pop + temp_sd, data = pca_data, cor=TRUE)
x$scores
x$loadings ## see directions (+/-) == intercorrelated on each component
plot(x$scores[,1], x$scores[,2], pch = 16)
plot(x$scores[,1], x$scores[,2], col = temp_col, pch = 16)

pca_data <- subset(data, pop != 'NA')
pca_data <- pca_data[, c(3, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16)]

pca <- princomp(pca_data, cor=T)
pca$loadings
plot(pca$scores[,1], pca$scores[,2])

pc <- prcomp(pca_data)
pc$rotation

sort.temp <- data[order(data$temp_mean) , ]
temp_col = heat.colors(length(data$temp_mean))
par(mar=c(5,5,4,2)) #make margins wider so there's space for larger than default text
plot(log10(data$Gross_emiss_1_2_3)~log10(data$pop), 
     xlab = 'School population (students, faculty, and staff)',
     ylab = 'Gross emissions (metric tons CO2)',
     main = 'Gross emissions of US colleges by population size',
     pch=16, bg = "red",
     yaxt = "n",
     xaxt = "n",
     cex.lab = 1.8, cex = 1.5, cex.main = 2)
axis(1, at = seq(1.5, 5, .5), labels = c("10^1.5", "10^2", "10^2.5", "10^3", "10^3.5", "10^4", "10^4.5", "10^5"), cex.lab = .75)
axis(2, at = seq(2.5, 6.5, .5), labels = c("10^2.5", "10^3", "10^3.5", "10^4", "10^4.5", "10^5", "10^5.5", "10^6", "10^6.5"))
sma(log10(data$Gross_emiss_1_2_3) ~ log10(data$pop), method = "OLS")
abline(a = 1.66107, b = 0.6912212, lwd = 1.25) #R^2 = 0.4371803; p-value = < 2.22e-16
legend(1.7, 6, legend = c("y = 0.69x + 1.66", "R^2 = 0.44", "p-value < 0.0001"),
       x.intersp = 0.65, y.intersp = 0.5) #reduces spacing in legend

Area_m <- (data$Sq_Ft*12*2.54)/100
plot(log10(Area_m), log10(data$Gross_emiss_1_2), xlab = "Area (m)", ylab = "Gross Emissions (minus commuting)", yaxt = "n", xaxt = "n", pch = 16, col = "darkgrey")
axis(1, at = seq(3, 7, 1), labels = c("10^3", "10^4", "10^5", "10^6", "10^7"), cex.lab = .75)
axis(2, at = seq(1, 6, 1), labels = c("10^1", "10^2", "10^3", "10^4", "10^5", "10^6"))
sma(log10(data$Gross_emiss_1_2) ~ log10(Area_m), method = "OLS")
abline(a = -2.153623, b = 1.125616, lwd = 1.25, col = "grey") #R^2 = 0.83, p-value = <2.22e-16
sma(log10(data$Gross_emiss_1_2) ~ log10(Area_m), method = "MA")
abline(a = -2.895142, b = 1.258034, lwd = 1.25, lty = 2, col = "grey") #R^2 = 0.83, p-value = <2.22e-16
legend(3, 6, lty = c(1, 2, 0, 0), col = c("grey", "grey"), legend = c("OLS y = 1.13x - 2.15","RMA y = 1.25x - 2.9", "R^2 = 0.83", "p-value = < 2.22e-16"))

plot(log10(Area_m), log10(data$Gross_emiss_1_2_3), xlab = "Area (m)", ylab = "Gross Emissions (plus commuting)", yaxt = "n", xaxt = "n", pch = 16, col = "grey")
axis(1, at = seq(3, 7, 1), labels = c("10^3", "10^4", "10^5", "10^6", "10^7"), cex.lab = .75)
axis(2, at = seq(3, 6, 1), labels = c("10^3", "10^4", "10^5", "10^6"))
sma(log10(data$Gross_emiss_1_2_3) ~ log10(Area_m), method = "OLS")
abline(a = -1.0760082, b = 0.9702347, lwd = 1.25, col = "grey") #R^2 = 0.78, p-value = <2.22e-16
sma(log10(data$Gross_emiss_1_2_3) ~ log10(Area_m), method = "MA")
abline(a = -1.880204, b = 1.113846, lwd = 1.25, lty = 2, col = "grey") #R^2 = 0.78, p-value = <2.22e-16
legend(3, 6, lty = c(1, 2, 0, 0), col = c("grey", "grey"), legend = c("OLS y = 0.97x - 1.07","RMA y = 1.11x - 1.88", "R^2 = 0.78", "p-value = < 2.22e-16"))

#Force through 0:
Area_m <- (data$Sq_Ft*12*2.54)/100
plot(log10(Area_m), log10(data$Gross_emiss_1_2), xlab = "Area (m)", ylab = "Gross Emissions (minus commuting)", yaxt = "n", xaxt = "n", pch = 16, col = "darkgrey")
axis(1, at = seq(3, 7, 1), labels = c("10^3", "10^4", "10^5", "10^6", "10^7"), cex.lab = .75)
axis(2, at = seq(1, 6, 1), labels = c("10^1", "10^2", "10^3", "10^4", "10^5", "10^6"))
sma(log10(data$Gross_emiss_1_2) ~ log10(Area_m) + 0, method = "OLS")
abline(a = 0, b = 0.7442304, lwd = 1.25, col = "grey") #R^2 = 0.99, p-value = 1
sma(log10(data$Gross_emiss_1_2) ~ log10(Area_m) + 0, method = "MA")
abline(a = 0, b = 7458319, lwd = 1.25, lty = 2, col = "grey") #R^2 = 0.99, p-value = 1
legend(3, 6, lty = c(1, 2, 0, 0), col = c("grey", "grey"), legend = c("OLS y = 0.74x","RMA y = 0.75x", "R^2 = 0.99", "p-value = 1"))

plot(log10(Area_m), log10(data$Gross_emiss_1_2_3), xlab = "Area (m)", ylab = "Gross Emissions (plus commuting)", yaxt = "n", xaxt = "n", pch = 16, col = "grey")
axis(1, at = seq(3, 7, 1), labels = c("10^3", "10^4", "10^5", "10^6", "10^7"), cex.lab = .75)
axis(2, at = seq(3, 6, 1), labels = c("10^3", "10^4", "10^5", "10^6"))
sma(log10(data$Gross_emiss_1_2_3) ~ log10(Area_m) + 0, method = "OLS")
abline(a = 0, b = 0.779684, lwd = 1.25, col = "grey") #R^2 = 0.99, p-value = 1
sma(log10(data$Gross_emiss_1_2_3) ~ log10(Area_m) + 0, method = "MA")
abline(a =0, b = 0.7809167, lwd = 1.25, lty = 2, col = "grey") #R^2 = 0.99, p-value = 1
legend(3, 6, lty = c(1, 2, 0, 0), col = c("grey", "grey"), legend = c("OLS y = 0.78x","RMA y = 0.78x", "R^2 = 0.99", "p-value = 1"))

#lat/long
all <- read.table("FIN.Alldata.csv", header = TRUE, sep = ",")
all2 <- subset(all, year.y >= 2005, select = c(college, Gross_emissions_.Scopes_1_._2_._3., hd2010.longitud.VL.Longitude.location.of.institution, hd2010.latitude.VL.Latitude.location.of.institution))
all3 <- subset(all2, !duplicated(college))
colnames(all3)[2] <- "Gross_Emiss_1_2_3"
colnames(all3)[3] <- "longitude"
colnames(all3)[4] <- "latitude"
all4 <- subset(all3, Gross_Emiss_1_2_3 != 2141531)
all5 <- subset(all4, Gross_Emiss_1_2_3 != 1949276)

plot(all5$longitude, log10(all5$Gross_Emiss_1_2_3), pch = 4, ylab = "Gross Emissions 1, 2, 3", xlab = "Longitude")
plot(all5$latitude, log10(all5$Gross_Emiss_1_2_3), pch = 3, ylab = "Gross Emissions 1, 2, 3", xlab = "Latitude")

##area v. pop
plot(log10(data1$pop), log10(data1$Sq_Ft), xlim = c(1.5, 5), ylim = c(3.5, 7.5), xlab = "Population", ylab = "Area")
sma(log10(data1$Sq_Ft) ~ log10(data1$pop), method = "OLS")
sma(log10(data1$Sq_Ft) ~ log10(data1$pop), method = "SMA")
abline(a = 4.085404, b = 0.5143555) #R^2 = 0.33
abline(a = 2.260089, b = 1.0005543, lty = 2) 
#a lot less space per person as get bigger

area_resid <- resid(lm(log10(data1$Sq_Ft)~log10(data1$pop)))

########NO COMMUTING############
##Without Commuting:
data <- read.table("FIN.Alldata.csv", header = TRUE, sep = ",")

require(lattice)
require(plyr)
require(smatr)

data2 <- subset(data, year.y >= 2005, select = c(college, Gross_emissions_.Scopes_1_._2., Gross_emissions_.Scopes_1_._2_._3., Net_emissions, Purchased_Electricity, Commuting, Gross_square_feet_of_building_space, Total_Student_Enrollment_.FTE., drvhr2010.tftestf.VL.Total.FTE.staff, drvef122010.undup.VL.12.month.unduplicated.headcount..total..2009.10, temp.F.mean, temp.F.sd, precip.sum.In, precip.days, rel.humid.mean, rel.humid.sd))

data_bin <- ddply(data2,"college",function(x)c(Gross_emiss_1_2 = median(x$Gross_emissions_.Scopes_1_._2.), Gross_emiss_1_2_3 = median(x$Gross_emissions_.Scopes_1_._2_._3.), Net_emiss = median(x$Net_emissions), Purch_Elec = median(x$Purchased_Electricity), Commute = median(x$Commuting), Sq_Ft = median(x$Gross_square_feet_of_building_space), Enroll = median(x$Total_Student_Enrollment_.FTE.), Fac_Staff = median(x$drvhr2010.tftestf.VL.Total.FTE.staff), pop = median(x$drvef122010.undup.VL.12.month.unduplicated.headcount..total..2009.10), temp_mean = median(x$temp.F.mean), temp_sd = median(x$temp.F.sd), precip_sum = median(x$precip.sum.In), precip_days = median(x$precip.days), humid_mean = median(x$rel.humid.mean), humid_sd = median(x$rel.humid.sd), n = nrow(x)))

data3 <- subset(data, year.y >= 2005, select = c(college, hd2010.locale.VL.Degree.of.urbanization..Urban.centric.locale., hd2010.ccsizset.VL.Carnegie.Classification.2010..Size.and.Setting, hd2010.control.VL.Control.of.institution, hd2010.hloffer.VL.Highest.level.of.offering, hd2010.stabbr.VL.State.abbreviation))

data4 <- subset(data3, !duplicated(college))
colnames(data4)[2] <- "urban"
colnames(data4)[3] <- "size"
colnames(data4)[4] <- "institution"
colnames(data4)[5] <- "degree"
colnames(data4)[6] <- "state"

density <- data_bin$pop/data_bin$Sq_Ft

Unit_C <- data_bin$Gross_emiss_1_2/data_bin$pop

data5 <- cbind(data_bin, data4, density, Unit_C)
data5 <- as.data.frame(data5)

#delete the two crazy outliers
data <- subset(data5, Gross_emiss_1_2_3!=25160678)
data <- subset(data, Gross_emiss_1_2_3!=5103644)

#write.csv(data, file = "Macro_data.csv")

anova(lm(data$Gross_emiss_1_2_3~data$pop+data$density))
anova(lm(data$Gross_emiss_1_2_3~data$pop+data$Sq_Ft))

data1 <- subset(data, pop!='NA')

resid2 <- resid(lm(data1$Gross_emiss_1_2~data1$pop))
plot(resid2~data1$density)

anova(lm(log10(data$Gross_emiss_1_2)~log10(data$pop)*log10(data$Sq_Ft)))
anova(lm(data$Gross_emiss_1_2~data$pop*data$Sq_Ft))
anova(lm(data$Gross_emiss_1_2~data$pop*data$Sq_Ft*density))
anova(lm(data$Gross_emiss_1_2~data$pop+data$Sq_Ft+density))
anova(lm(data$Gross_emiss_1_2~density))
anova(lm(data$pop~data$Sq_Ft))

plot(log10(data$Gross_emiss_1_2)~log10(data$Sq_Ft),
     xlab = 'College building area (square feet)',
     ylab = 'Gross emissions (metric tons CO2) (no commuting)',
     main = 'Gross emissions of US colleges by area',
     pch=21, bg = 'red',
     xaxt = "n", yaxt = "n",
     cex.lab = 1.8, cex = 1.5, cex.main = 2)
axis(1, at = seq(3.5, 7.5, .5), labels = c("10^3.5", "10^4", "10^4.5", "10^5", "10^5.5", "10^6", "10^6.5", "10^7", "10^7.5"))
axis(2, at = seq(2.5, 6.5, .5), labels = c("10^2.5", "10^3", "10^3.5", "10^4", "10^4.5", "10^5", "10^5.5", "10^6", "10^6.5"))
sma(log10(data$Gross_emiss_1_2) ~ log10(data$Sq_Ft), method = "OLS")
abline(a = -2.660287, b = 1.113757, lwd = 1.25) #R^2 = 0.8373587; p-value = < 2.22e-16
legend(4, 6, legend = c("y = 0.95x - 1.41", "R^2 = 0.77", "p-value < 0.0001"),
       x.intersp = 0.5, y.intersp = 0.5)

summary(lm(log10(data$Gross_emiss_1_2)~log10(data$Sq_Ft)+log10(data$pop)+data$temp_sd))
summary(lm(log10(data$Gross_emiss_1_2)~log10(data$Sq_Ft)+log10(data$pop)+data$temp_mean+data$temp_sd))
summary(lm(log10(data$Gross_emiss_1_2)~log10(data$Sq_Ft)+log10(data$pop)+data$temp_mean+data$temp_sd+data$precip_sum)) #precip not sig
summary(lm(log10(data$Gross_emiss_1_2)~log10(data$Sq_Ft)+log10(data$pop)+data$temp_mean+data$temp_sd+data$precip_days)) #precip days not sig
summary(lm(log10(data$Gross_emiss_1_2)~log10(data$Sq_Ft)+log10(data$pop)+data$temp_mean+data$temp_sd+data$humid_mean)) #humid mean not sig
summary(lm(log10(data$Gross_emiss_1_2)~log10(data$Sq_Ft)+log10(data$pop)+data$temp_mean+data$temp_sd+data$humid_sd)) #humid sd not sig
summary(lm(log10(data$Gross_emiss_1_2)~log10(data$Sq_Ft)+log10(data$pop)+data$temp_mean+data$temp_sd+data$urban)) #urban classification not sig - just "distant" is sig; positively correlated with emissions
summary(lm(log10(data$Gross_emiss_1_2)~log10(data$Sq_Ft)+log10(data$pop)+data$temp_mean+data$temp_sd+data$size)) #size not sig
summary(lm(log10(data$Gross_emiss_1_2)~log10(data$Sq_Ft)+log10(data$pop)+data$temp_mean+data$temp_sd+data$institution)) #institution not sig
summary(lm(log10(data$Gross_emiss_1_2)~log10(data$Sq_Ft)+log10(data$pop)+data$temp_mean+data$temp_sd+data$degree)) 
summary(lm(log10(data$Gross_emiss_1_2)~log10(data$Sq_Ft)+log10(data$pop)+data$temp_mean+data$temp_sd+data$state))

residuals2 <- resid(lm(log10(data1$Gross_emiss_1_2)~log10(data1$Sq_Ft)+log10(data1$pop)))
plot(residuals2~data1$temp_mean)
summary(lm(residuals2~data1$temp_sd)) #once pop and area are accounted for, temp sd only accounts for 3.7% of variation
summary(lm(residuals2~data1$temp_mean))

plot(residuals2~data1$temp_sd)

density <- data1$pop/data1$Sq_Ft

Unit_C <- data1$Gross_emiss_1_2/data1$pop
cbind(Unit_C, data1$college)

#comparison (table) rankings 1-20, rankings per capita 1-20, rankings based on residual values 1-20
residuals3 <- resid(lm(log10(data1$Gross_emiss_1_2)~log10(data1$Sq_Ft)+log10(data1$pop)))
data_resid <- cbind(data1, residuals3)
write.csv(data_resid, file = "no_commute_residual_data.csv")

Area_m <- (data$Sq_Ft*12*2.54)/100
plot(log10(Area_m), log10(data$Gross_emiss_1_2), xlab = "Area (m)", ylab = "Gross Emissions (minus commuting)", yaxt = "n", xaxt = "n", pch = 16, col = "darkgrey")
axis(1, at = seq(3, 7, 1), labels = c("10^3", "10^4", "10^5", "10^6", "10^7"), cex.lab = .75)
axis(2, at = seq(1, 6, 1), labels = c("10^1", "10^2", "10^3", "10^4", "10^5", "10^6"))
sma(log10(data$Gross_emiss_1_2) ~ log10(Area_m), method = "OLS")
abline(a = -2.153623, b = 1.125616, lwd = 1.25, col = "grey") #R^2 = 0.83, p-value = <2.22e-16
sma(log10(data$Gross_emiss_1_2) ~ log10(Area_m), method = "MA")
abline(a = -2.895142, b = 1.258034, lwd = 1.25, lty = 2, col = "grey") #R^2 = 0.83, p-value = <2.22e-16
legend(3, 6, lty = c(1, 2, 0, 0), col = c("grey", "grey"), legend = c("OLS y = 1.13x - 2.15","RMA y = 1.25x - 2.9", "R^2 = 0.83", "p-value = < 2.22e-16"))

plot(log10(Area_m), log10(data$Gross_emiss_1_2_3), pch = 21, bg = "red", xlab = "Area (m)", ylab = "Gross emissions (metric tons CO2)", cex.lab = 1.8, cex = 1.5, cex.main = 2, yaxt = "n", xaxt = "n", main = "Gross Emissions (1, 2, & 3) of US Colleges by Area")
axis(1, at = seq(3, 7, 1), labels = c("10^3", "10^4", "10^5", "10^6", "10^7"), cex.lab = .75)
axis(2, at = seq(3, 6, 1), labels = c("10^3", "10^4", "10^5", "10^6"))
sma(log10(data$Gross_emiss_1_2_3) ~ log10(Area_m), method = "OLS")
abline(a = -1.0760082, b = 0.9702347, lwd = 1.25, col = "grey") #R^2 = 0.78, p-value = <2.22e-16
sma(log10(data$Gross_emiss_1_2_3) ~ log10(Area_m), method = "MA")
abline(a = -1.880204, b = 1.113846, lwd = 1.25, lty = 2, col = "grey") #R^2 = 0.78, p-value =<2.22e-16
legend(3.5, 5.75, lty = c(1, 2, 0, 0), col = c("grey", "grey"), legend = c("OLS y = 0.97x - 1.07","RMA y = 1.11x - 1.88", "R^2 = 0.78", "p-value < 0.0001"))

plot(log10(Area_m), log10(data$Gross_emiss_1_2), pch = 21, bg = "red", xlab = "Area (m)", ylab = "Gross emissions (metric tons CO2)", cex.lab = 1.8, cex = 1.5, cex.main = 2, yaxt = "n", xaxt = "n", main = "Gross Emissions (1 & 2) of US Colleges by Area")
axis(1, at = seq(3, 7, 1), labels = c("10^3", "10^4", "10^5", "10^6", "10^7"), cex.lab = .75)
axis(2, at = seq(1, 6, 1), labels = c("10^1", "10^2", "10^3", "10^4", "10^5", "10^6"))
sma(log10(data$Gross_emiss_1_2) ~ log10(Area_m), method = "OLS")
abline(a = -2.149957, b = 1.124836, lwd = 1.25, col = "grey") #R^2 = 0.86, p-value = <2.22e-16
sma(log10(data$Gross_emiss_1_2) ~ log10(Area_m), method = "MA")
abline(a = -2.748246, b = 1.231535, lwd = 1.25, lty = 2, col = "grey") #R^2 = 0.86, p-value = <2.22e-16
legend(3.5, 5.75, lty = c(1, 2, 0, 0), col = c("grey", "grey"), legend = c("OLS y = 1.12x - 2.14","RMA y = 1.23x - 2.75", "R^2 = 0.86", "p-value < 0.0001"))


##Full data = "data" + tuition information
tuition <- read.table("master-list.csv", header = TRUE, sep = ",")
full_data <- merge(x = data, y = tuition, by.x = "college", by.y = "college")

GDP <- full_data$students*full_data$out_state
##pch = 20's can fill with background color
plot(log10(GDP), log10(full_data$Gross_emiss_1_2), pch = 21, bg = "red", xlab = "GDP", ylab = "Gross emissions (metric tons CO2)", cex.lab = 1.8, cex = 1.5, xaxt = "n", yaxt = "n", cex.main = 2, main = "Gross emissions (1 & 2) of US colleges by area")
sma(log10(full_data$Gross_emiss_1_2) ~ log10(GDP), method = "OLS") #R^2 = 0.26
sma(log10(full_data$Gross_emiss_1_2) ~ log10(GDP), method = "MA") #R^2 = 0.26
abline(a = -0.506615, b = 0.5864994, col = "grey", lwd = 1.25) #OLS
abline(a = -6.042983, b = 1.286337, col = "grey", lwd = 1.25, lty = 2) #RMA
legend(6.25, 2, legend = c("OLS y = -0.5 + 0.59x", "RMA y = -6 + 1.3x", "R^2 = 0.26", "p-value < 0.0001"), lty = c(1, 2))
axis(1, at = seq(6, 9, .5), labels = c("10^6", "10^6.5", "10^7", "10^7.5", "10^8", "10^8.5", "10^9"))
axis(2, at = seq(1, 5, 1), labels = c("10^1", "10^2", "10^3", "10^4", "10^5"))

##Color code by region (see ppt)
region_red <- subset(full_data, grepl(pattern = "TX|FL|LA|AL", state.y), select = c(Gross_emiss_1_2_3, Gross_emiss_1_2, out_state, students, state.y, pop, Sq_Ft))
region_orange <- subset(full_data, grepl(pattern = "NC|HI|CA|DC|GA|OK|SC|TN", state.y), select = c(Gross_emiss_1_2_3, Gross_emiss_1_2, out_state, students, state.y, pop, Sq_Ft))
region_yellow <- subset(full_data, grepl(pattern = "OR|AK|DE|KS|KY|MD|MO|NJ|VA|WV", state.y), select = c(Gross_emiss_1_2_3, Gross_emiss_1_2, out_state, students, state.y, pop, Sq_Ft))
region_green <- subset(full_data, grepl(pattern = "NV|UT|IL|IN|RI|MA|CT|NE|OH|PA|WA", state.y), select = c(Gross_emiss_1_2_3, Gross_emiss_1_2, out_state, students, state.y, pop, Sq_Ft))
region_blue <- subset(full_data, grepl(pattern = "MT|ND|SD|WY|MI|MN|VT|NH|ME|AK|CO|ID|IA|NY|WI", state.y), select = c(Gross_emiss_1_2_3, Gross_emiss_1_2, out_state, students, state.y, pop, Sq_Ft))


################################################

#write.csv(full_data, file = "full_data.csv")

regions <- list(red = "TX|FL|LA|AL", orange = "NC|HI|CA|DC|GA|OK|SC|TN", yellow = "OR|AK|DE|KS|KY|MD|MO|NJ|VA|WV", green = "NV|UT|IL|IN|RI|MA|CT|NE|OH|PA|WA", blue = "MT|ND|SD|WY|MI|MN|VT|NH|ME|AK|CO|ID|IA|NY|WI")
full_data = ldply(names(regions), function(x){
  pat = regions[[x]]
  ret = subset(full_data, grepl(pat, state.y))
  ret$Region = x
  return(ret)
})

#region_red <- subset(full_data, select = c(Gross_emiss_1_2, Gross_emiss_1_2_3, pop, college, state.y), subset = c(full_data$state.y == c("TX", "LA", "FL")), full_data$college == c("Valdosta State University"))
#region_orange <- subset(full_data, subset = c(Gross_emiss_1_2, Gross_emiss_1_2_3, pop, college, state.y), select = c(full_data$state.y == c("NC", "HI", "CA", "DC", "GA", "OK", "SC", "TN"), full_data$college == c("Auburn University", "University of Arizona")))
#region_yellow <- subset(full_data, subset = c(Gross_emiss_1_2, Gross_emiss_1_2_3, pop, college, state.y), select = c(full_data$state.y == c("OR", "AK", "DE", "KS", "KY", "MD", "MO", "NJ", "VA", "WV"), full_data$college == c("California State University Chico", "Butte College")))
#region_green <-  subset(full_data, subset = c(Gross_emiss_1_2, Gross_emiss_1_2_3, pop, college, state.y), select = c(full_data$state.y == c("NV", "UT", "IL", "IN", "RI", "MA", "CT", "NE", "OH", "PA", "WA"), full_data$college == c("Arizona State University", "Glendale Community College", "Mesa Community College", "Northern Arizona University", "Paradise Valley Community College", "Phoenix College", "Prescott College", "Rio Salado College", "Scottsdale Community College")))
#region_blue <-  subset(full_data, subset = c(Gross_emiss_1_2, Gross_emiss_1_2_3, pop, college, state.y), select = c(full_data$state.y == c("MT", "ND", "SD", "WY", "MI", "MN", "VT", "NH", "ME", "AK", "CO", "ID", "IA", "NY", "WI")))

sma(log10(region_red$Gross_emiss_1_2) ~ log10(region_red$pop), method = "OLS") #R^2 = 0.39
sma(log10(region_orange$Gross_emiss_1_2) ~ log10(region_orange$pop), method = "OLS") #R^2 = 0.15
sma(log10(region_yellow$Gross_emiss_1_2) ~ log10(region_yellow$pop), method = "OLS") #R^2 = 0.41
sma(log10(region_green$Gross_emiss_1_2) ~ log10(region_green$pop), method = "OLS") #R^2 = 0.24
sma(log10(region_blue$Gross_emiss_1_2) ~ log10(region_blue$pop), method = "OLS") #R^2 = 0.48

plot(log10(region_red$pop), log10(region_red$Gross_emiss_1_2), ylim = c(0, 5.5), xlim = c(1.5, 5), pch = 21, bg = "red", xlab = "Population", ylab = "Gross emissions (metric tons CO2)", cex.lab = 1.8, cex = 1.5, xaxt = "n", yaxt = "n", cex.main = 2, main = "Gross emissions (1 & 2) of US colleges by Region")
points(log10(region_orange$pop), log10(region_orange$Gross_emiss_1_2), ylim = c(0, 5.5), xlim = c(1.5, 5), pch = 21, bg = "orange")
points(log10(region_yellow$pop), log10(region_yellow$Gross_emiss_1_2), ylim = c(0, 5.5), xlim = c(1.5, 5), pch = 21, bg = "yellow")
points(log10(region_green$pop), log10(region_green$Gross_emiss_1_2), ylim = c(0, 5.5), xlim = c(1.5, 5), pch = 21, bg = "green")
points(log10(region_blue$pop), log10(region_blue$Gross_emiss_1_2), ylim = c(0, 5.5), xlim = c(1.5, 5), pch = 21, bg = "blue")
axis(1, at = seq(1.5, 4.5, .5), labels = c("10^1.5", "10^2", "10^2.5", "10^3", "10^3.5", "10^4", "10^4.5"))
axis(2, at = seq(0, 5.5, .5), labels = c("10^0", "10^0.5", "10^1", "10^1.5", "10^2", "10^2.5", "10^3", "10^3.5", "10^4", "10^4.5", "10^5", "10^5.5"))
abline(a = 2.0513209, b = 0.5482927, col= "red", lwd = 1.25, lty = 2)
abline(a = 2.532974, b = 0.4092382, col = "orange", lwd = 1.25, lty = 2)
abline(a = 1.7010224, b = 0.6684775, col = "yellow", lwd = 1.25, lty = 2)
abline(a = 1.2724359, b = 0.7253820, col = "green", lwd = 1.25, lty = 2)
abline(a = 1.3337166, b = 0.7374481, col = "blue", lwd = 1.25, lty = 2)
legend(1.5, 1, cex = 0.8, pch = rep(16, 5), col = c("red", "orange", "yellow", "green", "blue"), legend = c("b = 0.55, R^2 = 0.39", "b = 0.41, R^2 = 0.15", "b = 0.67, R^2 = 0.41", "b = 0.73, R^2 = 0.24", "b = 0.74, R^2 = 0.4"))

sma(log10(region_red$Gross_emiss_1_2_3) ~ log10(region_red$pop), method = "OLS") #R^2 = 0.41
sma(log10(region_orange$Gross_emiss_1_2_3) ~ log10(region_orange$pop), method = "OLS") #R^2 = 0.39
sma(log10(region_yellow$Gross_emiss_1_2_3) ~ log10(region_yellow$pop), method = "OLS") #R^2 = 0.54
sma(log10(region_green$Gross_emiss_1_2_3) ~ log10(region_green$pop), method = "OLS") #R^2 = 0.27
sma(log10(region_blue$Gross_emiss_1_2_3) ~ log10(region_blue$pop), method = "OLS") #R^2 = 0.55

plot(log10(region_red$pop), log10(region_red$Gross_emiss_1_2_3), ylim = c(2.5, 6), xlim = c(2, 5), pch = 21, bg = "red", xlab = "Population", ylab = "Gross emissions (metric tons CO2)", cex.lab = 1.8, xaxt = "n", yaxt = "n", cex.main = 2, main = "Gross emissions (1, 2, & 3) of US colleges by Region")
points(log10(region_orange$pop), log10(region_orange$Gross_emiss_1_2_3), ylim = c(2.5, 6), xlim = c(2, 5), pch = 21, bg = "orange")
points(log10(region_yellow$pop), log10(region_yellow$Gross_emiss_1_2_3), ylim = c(2.5, 6), xlim = c(2, 5), pch = 21, bg = "yellow")
points(log10(region_green$pop), log10(region_green$Gross_emiss_1_2_3), ylim = c(2.5, 6), xlim = c(2, 5), pch = 21, bg = "green")
points(log10(region_blue$pop), log10(region_blue$Gross_emiss_1_2_3), ylim = c(2.5, 6), xlim = c(2, 5), pch = 21, bg = "blue")
axis(1, at = seq(2, 5, .5), labels = c("10^2", "10^2.5", "10^3", "10^3.5", "10^4", "10^4.5", "10^5"))
axis(2, at = seq(2.5, 6, .5), labels = c("10^2.5", "10^3", "10^3.5", "10^4", "10^4.5", "10^5", "10^5.5", "10^6"))
abline(a = 2.0263074, b = 0.6475824, col= "red", lwd = 1.25, lty = 2)
abline(a = 1.785483, b = 0.6650402, col = "orange", lwd = 1.25, lty = 2)
abline(a = 1.577363, b = 0.7444914, col = "yellow", lwd = 1.25, lty = 2)
abline(a = 2.218166, b = 0.5416651, col = "green", lwd = 1.25, lty = 2)
abline(a = 1.690438, b = 0.6981503, col = "blue", lwd = 1.25, lty = 2)
legend(2, 6, cex = 0.8, pch = rep(16, 5), col = c("red", "orange", "yellow", "green", "blue"), legend = c("b = 0.65, R^2 = 0.41", "b = 0.67, R^2 = 0.39", "b = 0.74, R^2 = 0.54", "b = 0.54, R^2 = 0.27", "b = 0.7, R^2 = 0.55"))

##Stats for each region
anova(lm(log10(full_data$Gross_emiss_1_2_3)~log10(full_data$pop)*log10(full_data$Sq_Ft)*full_data$Region))
anova(lm(log10(full_data$Gross_emiss_1_2)~log10(full_data$pop)*log10(full_data$Sq_Ft)*full_data$Region))

summary(lm(log10(full_data$Gross_emiss_1_2_3)~log10(full_data$Sq_Ft)+log10(full_data$pop)+full_data$Region+full_data$temp_sd))
summary(lm(log10(full_data$Gross_emiss_1_2)~log10(full_data$Sq_Ft)+log10(full_data$pop)+full_data$Region+full_data$temp_sd))

boxplot(full_data$Sq_Ft~full_data$Region)

##Red/Blue States from 2008 election
party <- list(red = "AK|AZ|UT|ID|MT|WY|ND|SD|NE|KS|OK|TX|MO|AK|LA|MS|AL|TN|KY|WV|GA|SA", blue = "HI|CA|NV|OR|WA|CO|NM|MN|IO|WI|IL|ID|MI|OH|PA|VA|NC|NY|DC|RI|NH|VT|CN|ME|NJ|DL|MD|MA")
full_data = ldply(names(party), function(x){
  pat = party[[x]]
  ret = subset(full_data, grepl(pat, state.y))
  ret$Pol_Party = x
  return(ret)
})

anova(lm(log10(full_data$Gross_emiss_1_2_3)~log10(full_data$pop)*log10(full_data$Sq_Ft)*full_data$Pol_Party))
anova(lm(log10(full_data$Gross_emiss_1_2)~log10(full_data$pop)*log10(full_data$Sq_Ft)*full_data$Pol_Party))

summary(lm(log10(full_data$Gross_emiss_1_2_3)~log10(full_data$Sq_Ft)+log10(full_data$pop)+full_data$Pol_Party+full_data$temp_sd))
summary(lm(log10(full_data$Gross_emiss_1_2)~log10(full_data$Sq_Ft)+log10(full_data$pop)+full_data$Pol_Party+full_data$temp_sd))

sma(log10(full_data$pop[full_data$Pol_Party == "blue"]) ~ log10(full_data$Gross_emiss_1_2_3[full_data$Pol_Party == "blue"]), method = "OLS") #R^2 = 0.399
sma(log10(full_data$pop[full_data$Pol_Party == "red"]) ~ log10(full_data$Gross_emiss_1_2_3[full_data$Pol_Party == "red"]), method = "OLS") #R^2 = 0.45

sma(log10(full_data$pop[full_data$Pol_Party == "blue"]) ~ log10(full_data$Gross_emiss_1_2[full_data$Pol_Party == "blue"]), method = "OLS") #R^2 = 0.29
sma(log10(full_data$pop[full_data$Pol_Party == "red"]) ~ log10(full_data$Gross_emiss_1_2[full_data$Pol_Party == "red"]), method = "OLS") #R^2 = 0.35


##Figures for paper:
plot(log10(data$Gross_emiss_1_2_3)~log10(data$pop), 
     xlab = 'Population (students, faculty, and staff)',
     ylab = 'Gross emissions (metric tons CO2)',
     main = 'Gross emissions (1, 2, & 3) of US colleges by Population',
     pch=21, bg = "red",
     yaxt = "n",
     xaxt = "n",
     cex.lab = 1.8, cex = 1.5, cex.main = 2)
axis(1, at = seq(1.5, 5, .5), labels = c("10^1.5", "10^2", "10^2.5", "10^3", "10^3.5", "10^4", "10^4.5", "10^5"), cex.lab = .75)
axis(2, at = seq(2.5, 6.5, .5), labels = c("10^2.5", "10^3", "10^3.5", "10^4", "10^4.5", "10^5", "10^5.5", "10^6", "10^6.5"))
sma(log10(data$Gross_emiss_1_2_3) ~ log10(data$pop), method = "OLS")
abline(a = 1.490168, b = 0.7462532, lwd = 1.25, col = "grey") #R^2 = 0.4726371; p-value = < 2.22e-16
sma(log10(data$Gross_emiss_1_2_3) ~ log10(data$pop), method = "SMA")
abline(a = 0.1798928, b = 1.085481, lwd = 1.25, lty = 2, col = "grey")
legend(1.7, 6.4, cex = 0.8, lty = c(1, 2, 0, 0), col = c("grey", "grey"), legend = c("OLS y = 0.69x + 1.66", "RMA y = 1.08x + 0.18", "R^2 = 0.47", "p-value < 0.0001")) 

plot(log10(data$Gross_emiss_1_2)~log10(data$pop), 
     xlab = 'Population (students, faculty, and staff)',
     ylab = 'Gross emissions (metric tons CO2)',
     main = 'Gross emissions (1 & 2) of US colleges by Population',
     pch=21, bg = "red",
     yaxt = "n",
     xaxt = "n",
     cex.lab = 1.8, cex = 1.5, cex.main = 2)
axis(1, at = seq(1.5, 5, .5), labels = c("10^1.5", "10^2", "10^2.5", "10^3", "10^3.5", "10^4", "10^4.5", "10^5"), cex.lab = .75)
axis(2, at = seq(1, 6.5, .5), labels = c("10^1", "10^1.5", "10^2", "10^2.5", "10^3", "10^3.5", "10^4", "10^4.5", "10^5", "10^5.5", "10^6", "10^6.5"))
sma(log10(data$Gross_emiss_1_2) ~ log10(data$pop), method = "OLS")
abline(a = 1.3598246, b = 0.7245277, lwd = 1.25, col = "grey") #R^2 = 0.3547714; p-value = < 2.22e-16
sma(log10(data$Gross_emiss_1_2) ~ log10(data$pop), method = "SMA")
abline(a = -0.5400884, b = 1.216412, lwd = 1.25, lty = 2, col = "grey")
legend(1.63, 6.35, cex = 0.8, lty = c(1, 2, 0, 0), col = c("grey", "grey"), legend = c("OLS y = 0.72x + 1.35", "RMA y = 1.22x - 0.54", "R^2 = 0.47", "p-value < 0.0001")) 

plot(log10(Area_m), log10(data$Gross_emiss_1_2_3), pch = 21, bg = "red", xlab = "Area (m)", ylab = "Gross emissions (metric tons CO2)", cex.lab = 1.8, cex = 1.5, cex.main = 2, yaxt = "n", xaxt = "n", main = "Gross Emissions (1, 2, & 3) of US Colleges by Area")
axis(1, at = seq(3, 7, 1), labels = c("10^3", "10^4", "10^5", "10^6", "10^7"), cex.lab = .75)
axis(2, at = seq(3, 6, 1), labels = c("10^3", "10^4", "10^5", "10^6"))
sma(log10(data$Gross_emiss_1_2_3) ~ log10(Area_m), method = "OLS")
abline(a = -1.0760082, b = 0.9702347, lwd = 1.25, col = "grey") #R^2 = 0.78, p-value = <2.22e-16
sma(log10(data$Gross_emiss_1_2_3) ~ log10(Area_m), method = "SMA")
abline(a = -1.706097, b = 1.083819, lwd = 1.25, lty = 2, col = "grey") #R^2 = 0.78, p-value =<2.22e-16
legend(3.5, 5.75, cex = 0.8, lty = c(1, 2, 0, 0), col = c("grey", "grey"), legend = c("OLS y = 0.97x - 1.07","RMA y = 1.08x - 1.71", "R^2 = 0.78", "p-value < 0.0001"))

plot(log10(Area_m), log10(data$Gross_emiss_1_2), pch = 21, bg = "red", xlab = "Area (m)", ylab = "Gross emissions (metric tons CO2)", cex.lab = 1.8, cex = 1.5, cex.main = 2, yaxt = "n", xaxt = "n", main = "Gross Emissions (1 & 2) of US Colleges by Area")
axis(1, at = seq(3, 7, 1), labels = c("10^3", "10^4", "10^5", "10^6", "10^7"), cex.lab = .75)
axis(2, at = seq(1, 6, 1), labels = c("10^1", "10^2", "10^3", "10^4", "10^5", "10^6"))
sma(log10(data$Gross_emiss_1_2) ~ log10(Area_m), method = "OLS")
abline(a = -2.149957, b = 1.124836, lwd = 1.25, col = "grey") #R^2 = 0.86, p-value = <2.22e-16
sma(log10(data$Gross_emiss_1_2) ~ log10(Area_m), method = "SMA")
abline(a = -2.645575, b = 1.213225, lwd = 1.25, lty = 2, col = "grey") #R^2 = 0.86, p-value = <2.22e-16
legend(3.5, 5.75, cex = 0.8, lty = c(1, 2, 0, 0), col = c("grey", "grey"), legend = c("OLS y = 1.12x - 2.14","RMA y = 1.21x - 2.65", "R^2 = 0.86", "p-value < 0.0001"))

plot(log10(GDP), log10(full_data$Gross_emiss_1_2_3), pch = 21, bg = "red", xlab = "Out-of-State Tuition", ylab = "Gross emissions (metric tons CO2)", cex.lab = 1.8, cex = 1.5, xaxt = "n", yaxt = "n", cex.main = 2, main = "Gross emissions (1, 2, & 3) of US colleges by Out-of-State Tuition")
sma(log10(full_data$Gross_emiss_1_2_3) ~ log10(GDP), method = "OLS") #R^2 = 0.27
sma(log10(full_data$Gross_emiss_1_2_3) ~ log10(GDP), method = "SMA") #R^2 = 0.27
abline(a = 0.1881199, b = 0.5290554, col = "grey", lwd = 1.25) #OLS
abline(a = -3.692976, b = 1.0197549, col = "grey", lwd = 1.25, lty = 2) #RMA
legend(6, 5.5, legend = c("OLS y = 0.53x + 0.19", "RMA y = 1.02x - 3.69", "R^2 = 0.27", "p-value < 0.0001"), lty = c(1, 2, 0, 0), col = c("grey", "grey"), cex = 0.8)
axis(1, at = seq(6, 9, .5), labels = c("10^6", "10^6.5", "10^7", "10^7.5", "10^8", "10^8.5", "10^9"))
axis(2, at = seq(2.5, 6.5, .5), labels = c("10^2.5", "10^3", "10^3.5", "10^4", "10^4.5", "10^5", "10^5.5", "10^5", "10^6.5"))

plot(log10(GDP), log10(full_data$Gross_emiss_1_2), pch = 21, bg = "red", xlab = "Out-of-State Tuition", ylab = "Gross emissions (metric tons CO2)", cex.lab = 1.8, cex = 1.5, xaxt = "n", yaxt = "n", cex.main = 2, main = "Gross emissions (1 & 2) of US colleges by Out-of-State Tuition")
sma(log10(full_data$Gross_emiss_1_2) ~ log10(GDP), method = "OLS") #R^2 = 0.30
sma(log10(full_data$Gross_emiss_1_2) ~ log10(GDP), method = "SMA") #R^2 = 0.30
abline(a = -0.506615, b = 0.5864994, col = "grey", lwd = 1.25) #OLS
abline(a = -4.355905, b = 1.0779669, col = "grey", lwd = 1.25, lty = 2) #RMA
legend(6.3, 2, legend = c("OLS y = 0.59x - 0.5", "RMA y = 1.08x - 4.36", "R^2 = 0.30", "p-value < 0.0001"), lty = c(1, 2, 0, 0), col = c("grey", "grey"), cex = 0.8)
axis(1, at = seq(6, 9, .5), labels = c("10^6", "10^6.5", "10^7", "10^7.5", "10^8", "10^8.5", "10^9"))
axis(2, at = seq(1, 5, 1), labels = c("10^1", "10^2", "10^3", "10^4", "10^5"))

plot(log10(region_red$pop), log10(region_red$Gross_emiss_1_2), ylim = c(0, 5.5), xlim = c(1.5, 5), pch = 21, bg = "red", xlab = "Population", ylab = "Gross emissions (metric tons CO2)", cex.lab = 1.8, cex = 1.5, xaxt = "n", yaxt = "n", cex.main = 2, main = "Gross emissions (1 & 2) of US colleges by Region")
points(log10(region_orange$pop), log10(region_orange$Gross_emiss_1_2), ylim = c(0, 5.5), xlim = c(1.5, 5), pch = 21, bg = "orange")
points(log10(region_yellow$pop), log10(region_yellow$Gross_emiss_1_2), ylim = c(0, 5.5), xlim = c(1.5, 5), pch = 21, bg = "yellow")
points(log10(region_green$pop), log10(region_green$Gross_emiss_1_2), ylim = c(0, 5.5), xlim = c(1.5, 5), pch = 21, bg = "green")
points(log10(region_blue$pop), log10(region_blue$Gross_emiss_1_2), ylim = c(0, 5.5), xlim = c(1.5, 5), pch = 21, bg = "blue")
axis(1, at = seq(1.5, 4.5, .5), labels = c("10^1.5", "10^2", "10^2.5", "10^3", "10^3.5", "10^4", "10^4.5"))
axis(2, at = seq(0, 5.5, .5), labels = c("10^0", "10^0.5", "10^1", "10^1.5", "10^2", "10^2.5", "10^3", "10^3.5", "10^4", "10^4.5", "10^5", "10^5.5"))
abline(a = 2.0513209, b = 0.5482927, col= "red", lwd = 1.25, lty = 2)
abline(a = 2.532974, b = 0.4092382, col = "orange", lwd = 1.25, lty = 2)
abline(a = 1.7010224, b = 0.6684775, col = "yellow", lwd = 1.25, lty = 2)
abline(a = 1.2724359, b = 0.7253820, col = "green", lwd = 1.25, lty = 2)
abline(a = 1.3337166, b = 0.7374481, col = "blue", lwd = 1.25, lty = 2)
legend(1.5, 1, cex = 0.8, pch = rep(16, 5), col = c("red", "orange", "yellow", "green", "blue"), legend = c("b = 0.55, R^2 = 0.39", "b = 0.41, R^2 = 0.15", "b = 0.67, R^2 = 0.41", "b = 0.73, R^2 = 0.24", "b = 0.74, R^2 = 0.4"))

plot(log10(region_red$pop), log10(region_red$Gross_emiss_1_2_3), ylim = c(2.5, 6), xlim = c(2, 5), pch = 21, bg = "red", xlab = "Population", ylab = "Gross emissions (metric tons CO2)", cex.lab = 1.8, xaxt = "n", yaxt = "n", cex.main = 2, main = "Gross emissions (1, 2, & 3) of US colleges by Region")
points(log10(region_orange$pop), log10(region_orange$Gross_emiss_1_2_3), ylim = c(2.5, 6), xlim = c(2, 5), pch = 21, bg = "orange")
points(log10(region_yellow$pop), log10(region_yellow$Gross_emiss_1_2_3), ylim = c(2.5, 6), xlim = c(2, 5), pch = 21, bg = "yellow")
points(log10(region_green$pop), log10(region_green$Gross_emiss_1_2_3), ylim = c(2.5, 6), xlim = c(2, 5), pch = 21, bg = "green")
points(log10(region_blue$pop), log10(region_blue$Gross_emiss_1_2_3), ylim = c(2.5, 6), xlim = c(2, 5), pch = 21, bg = "blue")
axis(1, at = seq(2, 5, .5), labels = c("10^2", "10^2.5", "10^3", "10^3.5", "10^4", "10^4.5", "10^5"))
axis(2, at = seq(2.5, 6, .5), labels = c("10^2.5", "10^3", "10^3.5", "10^4", "10^4.5", "10^5", "10^5.5", "10^6"))
abline(a = 2.0263074, b = 0.6475824, col= "red", lwd = 1.25, lty = 2)
abline(a = 1.785483, b = 0.6650402, col = "orange", lwd = 1.25, lty = 2)
abline(a = 1.577363, b = 0.7444914, col = "yellow", lwd = 1.25, lty = 2)
abline(a = 2.218166, b = 0.5416651, col = "green", lwd = 1.25, lty = 2)
abline(a = 1.690438, b = 0.6981503, col = "blue", lwd = 1.25, lty = 2)
legend(2, 6, cex = 0.8, pch = rep(16, 5), col = c("red", "orange", "yellow", "green", "blue"), legend = c("b = 0.65, R^2 = 0.41", "b = 0.67, R^2 = 0.39", "b = 0.74, R^2 = 0.54", "b = 0.54, R^2 = 0.27", "b = 0.7, R^2 = 0.55"))

plot(log10(full_data$Gross_emiss_1_2_3[full_data$Pol_Party == "blue"]), log10(full_data$pop[full_data$Pol_Party == "blue"]), pch = 21, bg = "blue", xaxt = "n", yaxt = "n", xlab = "Population", ylab = "Gross emissions (metric tons CO2)", main = "Gross Emissions (1, 2, & 3) of US Colleges by Political Party", cex.lab = 1.8, cex.main = 2, xlim = c(2.5, 6), ylim = c(2, 5))
points(log10(full_data$Gross_emiss_1_2_3[full_data$Pol_Party == "red"]), log10(full_data$pop[full_data$Pol_Party == "red"]), xlim = c(2.5, 6), ylim = c(2, 5), pch = 21, bg = "red")
axis(1, at = seq(2.5, 6, .5), labels = c("10^2.5", "10^3", "10^3.5", "10^4", "10^4.5", "10^5", "10^5.5", "10^6"))
axis(2, at = seq(2, 5, .5), labels = c("10^2", "10^2.5", "10^3", "10^3.5", "10^4", "10^4.5", "10^5"))
abline(a = 1.1377390, b = 0.6270436, lwd = 1.25, lty = 2, col = "blue")
abline(a = 0.7719291, b = 0.6763388, lwd = 1.25, lty = 2, col = "red")
legend(4, 2.5, cex = 0.8, pch = rep(16, 2), col = c("blue", "red"), legend = c("b = 0.63, R^2 = 0.4", "b = 0.68, R^2 = 0.45"))

plot(log10(full_data$Gross_emiss_1_2[full_data$Pol_Party == "blue"]), log10(full_data$pop[full_data$Pol_Party == "blue"]), pch = 21, bg = "blue", xaxt = "n", yaxt = "n", xlab = "Population", ylab = "Gross emissions (metric tons CO2)", main = "Gross Emissions (1 & 2) of US Colleges by Political Party", cex.lab = 1.8, cex.main = 2, xlim = c(1, 6), ylim = c(2, 5))
points(log10(full_data$Gross_emiss_1_2[full_data$Pol_Party == "red"]), log10(full_data$pop[full_data$Pol_Party == "red"]), xlim = c(1, 6), ylim = c(2, 5), pch = 21, bg = "red")
axis(1, at = seq(1, 6, .5), labels = c("10^1", "10^1.5", "10^2", "10^2.5", "10^3", "10^3.5", "10^4", "10^4.5", "10^5", "10^5.5", "10^6"))
axis(2, at = seq(2, 5, .5), labels = c("10^2", "10^2.5", "10^3", "10^3.5", "10^4", "10^4.5", "10^5"))
abline(a = 1.995102, b = 0.4528108, lwd = 1.25, lty = 2, col = "blue")
abline(a = 1.1428772, b = 0.6254081, lwd = 1.25, lty = 2, col = "red")
legend(4.5, 2.5, cex = 0.8, pch = rep(16, 2), col = c("blue", "red"), legend = c("b = 0.45, R^2 = 0.29", "b = 0.63, R^2 = 0.35"))

##Any plotting (of residuals) is data1 (removed NA)
##"data" has NA's
```