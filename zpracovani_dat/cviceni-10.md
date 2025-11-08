title: CV10 – Úvod do geostatistiky
layout: default
description: Geostatistická EDA, normalita vs. lognormalita, semivariogram (izotropní i směrový), výběr teoretického modelu a krigování v R.
---

# CV10 – Úvod do geostatistiky

Metodický návod krok za krokem (R 4.x). Zaměřuje se na:
- vizualizaci studované oblasti a hodnot,
- EDA pro původní i logaritmované hodnoty,
- histogramy a rankitové (QQ) grafy,
- izotropní a směrové semivariogramy, volbu teoretického modelu,
- základní (ordinary) krigování.

---

## Vstupní data

**Soubor:** `Doubrava.xls` ke stažení zde: [📁 Otevřít složku download na GitHubu](https://github.com/NikolaK97/nikolak97.github.io/tree/main/download)  
**Proměnná pro analýzu:** `Kal` (obsah aleuropelitické frakce, %)

**Sloupce:**
- `X`, `Y` – relativní souřadnice v metrech (rovinné)
- `Pisek` – % písčité frakce (> 0.063 mm)
- `ALPE` – % prachové frakce (0.004–0.063 mm)
- `Kal` – % aleuropelitické frakce (< 0.004 mm) – hlavní analyzovaná veličina
- `Ad` – popelnatost bezvodého vzorku v %
- `W` – vlhkost v %
- `S` – obsah síry v %

---

## Předpoklady a instalace balíčků v R

```r
setwd("D:/PAD/Geostatistika")
install.packages(c("readxl","ggplot2","scales","moments","car","sp","gstat","lattice","dplyr"))
library(readxl); library(ggplot2); library(scales); library(moments)
library(car); library(sp); library(gstat); library(lattice); library(dplyr)
```

---

## 1. Import dat

```r
Doubrava <- readxl::read_excel("Doubrava.xls", sheet = 1)
str(Doubrava); summary(Doubrava)
Doubrava <- Doubrava %>% filter(!is.na(Kal), !is.na(X), !is.na(Y))
```

---

## 2. Vykreslení plochy studované oblasti

```r
brks <- c(quantile(Doubrava$Kal, c(0, .25, .5, .75, 1), na.rm = TRUE))
Doubrava$Kal_cat <- cut(Doubrava$Kal, breaks = unique(brks), include.lowest = TRUE)
ggplot(Doubrava, aes(x = X, y = Y)) +
  geom_point(aes(color = Kal_cat), size = 2) +
  coord_equal() + scale_color_brewer(palette = "YlGnBu", name = "Kal (%)") +
  labs(title = "Rozložení hodnot Kal v prostoru", x = "X [m]", y = "Y [m]") +
  theme_minimal()
```

---

## 3. Explorační analýza dat

### 3.1 Základní statistiky a histogram

```r
hist(Doubrava$Kal, breaks = "FD", col = "gray85", main = "Histogram Kal")
summary(Doubrava$Kal); skewness(Doubrava$Kal); kurtosis(Doubrava$Kal)
```

### 3.2 Test normality a QQ graf

```r
shapiro.test(Doubrava$Kal)
qqPlot(Doubrava$Kal, main = "QQ plot – Kal (původní)")
```

### 3.3 Log-transformace

```r
Doubrava$Kal_log <- log(Doubrava$Kal)
hist(Doubrava$Kal_log, breaks = "FD", col = "gray85", main = "Histogram log(Kal)")
summary(Doubrava$Kal_log)
shapiro.test(Doubrava$Kal_log)
qqPlot(Doubrava$Kal_log, main = "QQ plot – log(Kal)")
```

---

## 4. Kvantily a typ distribuce

```r
quantile(Doubrava$Kal, probs = c(.01,.05,.1,.25,.5,.75,.9,.95,.99), na.rm = TRUE)
quantile(Doubrava$Kal_log, probs = c(.01,.05,.1,.25,.5,.75,.9,.95,.99), na.rm = TRUE)
```

---

## 5. Příprava prostorového objektu

```r
oblast.sp <- Doubrava
coordinates(oblast.sp) <- ~ X + Y
proj4string(oblast.sp) <- CRS(as.character(NA))
```

---

## 6. Semivariogramy

### 6.1 Semivariační mrak

```r
vario.cloud <- variogram(Kal ~ 1, oblast.sp, cloud = TRUE)
plot(vario.cloud, main = "Semivariační mrak – Kal")
```

### 6.2 Izotropní semivariogram

```r
vario <- variogram(Kal ~ 1, oblast.sp, width=50, cutoff=800)
plot(vario, main = "Izotropní semivariogram – Kal")
```

### 6.3 Směrové semivariogramy

```r
vario.aniso <- variogram(Kal ~ 1, oblast.sp, alpha = c(0, 45, 90, 135), width=50, cutoff=800)
plot(vario.aniso, main = "Směrové semivariogramy – Kal")
```

---

## 7. Teoretické modely a fit

```r
m_try <- vgm(250, "Gau", 400, 20)
plot(vario, m_try, main = "Ruční Gaussian model")

m_fit_sph <- fit.variogram(vario, vgm(model = "Sph", psill = 200, range = 300, nugget = 10))
m_fit_exp <- fit.variogram(vario, vgm(model = "Exp", psill = 200, range = 300, nugget = 10))
m_fit_gau <- fit.variogram(vario, vgm(model = "Gau", psill = 200, range = 300, nugget = 10))
plot(vario, m_fit_sph, main = "Fit Spherical")
plot(vario, m_fit_exp, main = "Fit Exponential")
plot(vario, m_fit_gau, main = "Fit Gaussian")
```

---

## 8. Směrové fitování a anizotropie

```r
vario_0 <- variogram(Kal ~ 1, oblast.sp, alpha = 0, width = 50, cutoff = 800)
vario_90 <- variogram(Kal ~ 1, oblast.sp, alpha = 90, width = 50, cutoff = 800)
fit_0 <- fit.variogram(vario_0, vgm(model = "Sph", psill = 200, range = 300, nugget = 10))
fit_90 <- fit.variogram(vario_90, vgm(model = "Sph", psill = 200, range = 300, nugget = 10))
plot(vario_0, fit_0, main = "Směr 0° – Spherical fit")
plot(vario_90, fit_90, main = "Směr 90° – Spherical fit")
```

---

## 9. Krigování

```r
x.range <- as.integer(range(Doubrava$X, na.rm = TRUE))
y.range <- as.integer(range(Doubrava$Y, na.rm = TRUE))
grd <- expand.grid(x=seq(from=x.range[1], to=x.range[2], by=5),
                   y=seq(from=y.range[1], to=y.range[2], by=5))
coordinates(grd) <- ~ x + y
gridded(grd) <- TRUE

ok <- krige(Kal ~ 1, oblast.sp, grd, model = m_fit_sph)
spplot(ok["var1.pred"], main = "Predikovaná hodnota – krigování")
spplot(ok["var1.var"], main = "Rozptyl predikce – krigování")
```

---

## 10. Cross-validation

```r
cv <- krige.cv(Kal ~ 1, oblast.sp, model = m_fit_sph, nfold = nrow(oblast.sp))
ME <- mean(cv$residual, na.rm = TRUE)
MAE <- mean(abs(cv$residual), na.rm = TRUE)
RMSE <- sqrt(mean(cv$residual^2, na.rm = TRUE))
R2 <- cor(cv$observed, cv$var1.pred, use = "complete.obs")^2
data.frame(ME, MAE, RMSE, R2)
qqPlot(cv$residual, main = "QQ plot – rezidua LOOCV")
```

---

## 11. Shrnutí a interpretace

- Vyhodnoťte normální vs. lognormální rozdělení (`Kal` vs. `log(Kal)`).
- Porovnejte tvar histogramů a QQ grafů.
- Zhodnoťte izotropní a směrové semivariogramy, určete typ anizotropie.
- Popište vybraný teoretický model (nugget, sill, range).
- Uveďte mapu predikcí a rozptylu z krigování.
- Zhodnoťte přesnost podle ME, MAE, RMSE, R².

---
