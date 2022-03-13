library(readr) 
dax <- read_csv("data/^dax_d.csv")
dax<-dax[,c(1,5)]
ftse <- read_csv("data/^ftm_d.csv")
ftse<-ftse[,c(1,5)]
shc <- read_csv("data/^shc_d.csv")
shc<-shc[,c(1,5)]
spx <- read_csv("data/^spx_d.csv")
spx<-spx[,c(1,5)]
wig20 <- read_csv("data/wig20_d.csv")
wig20<-wig20[,c(1,5)]


#merge(dax, wig20, by=dax$Data)

#utworzenie jednego data frame, na w miejscu braku obserwacji
library(dplyr)

daxFTSE<-full_join(dax, ftse, by="Data")
dfs<-full_join(daxFTSE, shc, by="Data")
dfss<-full_join(dfs, spx, by="Data")
dfssw<-full_join(dfss, wig20, by="Data")

data<-dfssw
colnames(data)<-c("Data", "DAX", "FTSE", "SHC", "SPX", "WIG20")
sum(is.na(data))

data<-arrange(data, Data)
sum(is.na(data))

#interpolacja
library(zoo)


DAX_no_NA<-na.approx(data$DAX, na.rm = FALSE)
FTSE_no_NA<-na.approx(data$FTSE, na.rm = FALSE)
SHC_no_NA<-na.approx(data$SHC, na.rm = FALSE)
SPX_no_NA<-na.approx(data$SPX, na.rm = FALSE)
WIG20_no_NA<-na.approx(data$WIG20, na.rm = FALSE)


daneN<-data.frame("Data"=data$Data, "DAX"=DAX_no_NA, "FTSE"=FTSE_no_NA, "SHC"=SHC_no_NA, "SPX"=SPX_no_NA, "WIG20"=WIG20_no_NA)
daneN<-daneN[1:2585,]
sum(is.na(daneN))

stopy<-data.frame(0)
stopy <- data.frame(daneN$Data[2: nrow(daneN)])


for(i in 2:(nrow(daneN)))
{
  stopy[i-1,2]=log(daneN[i,2]/daneN[i-1,2])
  stopy[i-1,3]=log(daneN[i,3]/daneN[i-1,3])
  stopy[i-1,4]=log(daneN[i,4]/daneN[i-1,4])
  stopy[i-1,5]=log(daneN[i,5]/daneN[i-1,5])
  stopy[i-1,6]=log(daneN[i,6]/daneN[i-1,6])
}
colnames(stopy)<-c("Data", "DAX", "FTSE", "SHC", "SPX", "WIG20")
stopy
stopy<-stopy[,2:6]

#zbadanie stacjonarności stóp zwrotu
library(tseries)
adf.test(stopy[,1],alternative = c("stationary"))
adf.test(stopy[,2],alternative = c("stationary"))
adf.test(stopy[,3],alternative = c("stationary"))
adf.test(stopy[,4],alternative = c("stationary"))
adf.test(stopy[,5],alternative = c("stationary"))

#p-value mniejsze od poziomu istotoności, odrzucamy hipotezę zerową mówiącą, o niestacjonarnosci

#wybór rzędu opóźnień
stopy

library(vars)

VARselect(stopy)


#funkcja oblicza kryteria informacyjne dla zmiennych 
#do modelu wybieramy ten rząd gdzie wart wybranego kryterium jest najmniejsza
#oznacza to,że  utrata informacji jest najmniejsza

#według dwóch kryteriów rząd opóźnień to 1, utworzę model z takim opóźnieniem

var1 <- VAR(stopy, p=1)

summary(var1)

#sprawdzenie czy reszty mają rozkład normalny i czy nie wykazują autokorelacji

Box.test(var1$varresult$DAX$residuals, lag =1, type=c("Ljung-Box"))
Box.test(var1$varresult$FTSE$residuals, lag =1, type=c("Ljung-Box"))
Box.test(var1$varresult$SHC$residuals, lag =1, type=c("Ljung-Box"))
Box.test(var1$varresult$SPX$residuals, lag =1, type=c("Ljung-Box"))
Box.test(var1$varresult$WIG20$residuals, lag =1, type=c("Ljung-Box"))

#H0: Autokorelacja reszt nie wystepuje
#H1: Autokorelcja wystepuje

shapiro.test(var1$varresult$DAX$residuals)
shapiro.test(var1$varresult$FTSE$residuals)
shapiro.test(var1$varresult$SHC$residuals)
shapiro.test(var1$varresult$SPX$residuals)
shapiro.test(var1$varresult$WIG20$residuals)

# POMYŚLEĆ O VAR2, SPOWODOWANE TO JEST CZUŁOŚCIĄ KRYTERIÓW

var2 <- VAR(stopy, p=2)
#sprawdzenie czy reszty mają rozkład normalny i czy nie wykazują autokorelacji

Box.test(var2$varresult$DAX$residuals, lag =2, type=c("Ljung-Box"))
Box.test(var2$varresult$FTSE$residuals, lag =2, type=c("Ljung-Box"))
Box.test(var2$varresult$SHC$residuals, lag =2, type=c("Ljung-Box"))
Box.test(var2$varresult$SPX$residuals, lag =2, type=c("Ljung-Box"))
Box.test(var2$varresult$WIG20$residuals, lag =2, type=c("Ljung-Box"))

#H0: Autokorelacja reszt nie wystepuje
#H1: Autokorelcja wystepuje

shapiro.test(var2$varresult$DAX$residuals)
shapiro.test(var2$varresult$FTSE$residuals)
shapiro.test(var2$varresult$SHC$residuals)
shapiro.test(var2$varresult$SPX$residuals)
shapiro.test(var2$varresult$WIG20$residuals)

#H0: brak przyczynowosci w sensie Grangera - beta = 0
#H1 ~ H0
causality(var2, cause = "DAX")$Granger
causality(var2, cause = "FTSE")$Granger
causality(var2, cause = "SHC")$Granger
causality(var2, cause = "SPX")$Granger
causality(var2, cause = "WIG20")$Granger

#INTERPRETOWAĆ gwiazdki w modelu przy 
#GRANGER TEST

summary(var2)
#test restrykcji - to juz nie potrzebne
restrict <- matrix(c(1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
                     1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
                     1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
                     1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
                     1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0),
                   nrow=5, ncol=11, byrow=TRUE)
restrict(var2, method ="man", resmat = restrict)

  
