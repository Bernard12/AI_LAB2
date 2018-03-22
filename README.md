Загрузка необходимых пакетов

    require(tidyverse)
    require(Quandl)

№1
==

Загрузка данных

    d <- as_tibble(Quandl("WIKI/GOOGL"))
    d <- d %>% arrange(Close)

Регрессия для Close от всех возможных параметров (возьмем первые 75%
записей)

    trainData <- d %>%
        select(1:8) %>%
        slice(1:(9*nrow(.) %/% 10))

    testData <-  d %>%
        select(1:8) %>%
        slice(((9*nrow(.) %/% 10) + 1):nrow(.))

    fit <- lm(Close ~ . ,data = trainData)
    summary(fit)

    ## 
    ## Call:
    ## lm(formula = Close ~ ., data = trainData)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -20.4786  -1.8238   0.0019   1.7915  20.9886 
    ## 
    ## Coefficients: (1 not defined because of singularities)
    ##                 Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   -2.466e+00  1.099e+00  -2.244   0.0249 *  
    ## Date           1.692e-04  8.318e-05   2.034   0.0420 *  
    ## Open          -5.468e-01  1.402e-02 -38.993   <2e-16 ***
    ## High           7.669e-01  1.371e-02  55.939   <2e-16 ***
    ## Low            7.800e-01  1.168e-02  66.808   <2e-16 ***
    ## Volume         1.703e-08  1.038e-08   1.640   0.1010    
    ## `Ex-Dividend` -1.399e-02  5.763e-03  -2.427   0.0153 *  
    ## `Split Ratio`         NA         NA      NA       NA    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 3.268 on 3062 degrees of freedom
    ## Multiple R-squared:  0.9997, Adjusted R-squared:  0.9997 
    ## F-statistic: 1.464e+06 on 6 and 3062 DF,  p-value: < 2.2e-16

Для параметра Close статистически значимо влияют все параметры кроме
Volume, так что можно убрать его

    fit <- lm(Close ~ . - Volume,data = trainData)
    summary(fit)

    ## 
    ## Call:
    ## lm(formula = Close ~ . - Volume, data = trainData)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -20.5042  -1.7973  -0.0075   1.7696  20.6786 
    ## 
    ## Coefficients: (1 not defined because of singularities)
    ##                 Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   -1.390e+00  8.822e-01  -1.576   0.1151    
    ## Date           1.066e-04  7.392e-05   1.442   0.1494    
    ## Open          -5.468e-01  1.403e-02 -38.982   <2e-16 ***
    ## High           7.737e-01  1.308e-02  59.144   <2e-16 ***
    ## Low            7.731e-01  1.090e-02  70.937   <2e-16 ***
    ## `Ex-Dividend` -1.411e-02  5.764e-03  -2.448   0.0144 *  
    ## `Split Ratio`         NA         NA      NA       NA    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 3.269 on 3063 degrees of freedom
    ## Multiple R-squared:  0.9997, Adjusted R-squared:  0.9997 
    ## F-statistic: 1.755e+06 on 5 and 3063 DF,  p-value: < 2.2e-16

Без Volume не влияет параметр Date

    fit <- lm(Close ~ . - Volume - Date,data = trainData)
    summary(fit)

    ## 
    ## Call:
    ## lm(formula = Close ~ . - Volume - Date, data = trainData)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -20.5690  -1.7957  -0.0178   1.7844  20.7299 
    ## 
    ## Coefficients: (1 not defined because of singularities)
    ##                Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   -0.153848   0.206821  -0.744   0.4570    
    ## Open          -0.546706   0.014030 -38.966   <2e-16 ***
    ## High           0.773130   0.013078  59.116   <2e-16 ***
    ## Low            0.774175   0.010876  71.180   <2e-16 ***
    ## `Ex-Dividend` -0.013898   0.005763  -2.412   0.0159 *  
    ## `Split Ratio`        NA         NA      NA       NA    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 3.27 on 3064 degrees of freedom
    ## Multiple R-squared:  0.9997, Adjusted R-squared:  0.9997 
    ## F-statistic: 2.193e+06 on 4 and 3064 DF,  p-value: < 2.2e-16

Получилась некоторая модель, в которой каждый параметр влияет на Close

    predicted <- predict(fit,newdata = testData)

    ## Warning in predict.lm(fit, newdata = testData): prediction from a rank-
    ## deficient fit may be misleading

    predicted <- as_tibble(predicted)
    predicted$pos <- 1:nrow(predicted)
    testData$pos <- 1:nrow(testData)

    ggplot(predicted,aes(x = pos, y = value))+
        geom_line(color = "red")

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-6-1.png)

    ggplot(testData,aes(x = pos, y = Close))+
        geom_line(color = "blue")

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-6-2.png)

    ggplot()+
        geom_line(data = predicted,aes(x = pos, y = value),color = "red")+
        geom_line(data = testData,aes(x = pos, y = Close),color = "blue")

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-6-3.png)

Если увеличить тестовые данные и уменьшить тренировочные до соотношения
1:1

    trainData <- d %>%
        select(1:8) %>%
        slice(1:(2*nrow(.) %/% 4))

    testData <-  d %>%
        select(1:8) %>%
        slice(((2*nrow(.) %/% 4) + 1):nrow(.))

Новая модель

    fit <- lm(Close ~ . - Volume - Date,data = trainData)
    summary(fit)

    ## 
    ## Call:
    ## lm(formula = Close ~ . - Volume - Date, data = trainData)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -21.4983  -1.6551  -0.0265   1.7391  20.0160 
    ## 
    ## Coefficients: (2 not defined because of singularities)
    ##               Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   -0.15284    0.29411   -0.52    0.603    
    ## Open          -0.56711    0.01899  -29.86   <2e-16 ***
    ## High           0.80909    0.01722   47.00   <2e-16 ***
    ## Low            0.75784    0.01500   50.52   <2e-16 ***
    ## `Ex-Dividend`       NA         NA      NA       NA    
    ## `Split Ratio`       NA         NA      NA       NA    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 3.133 on 1704 degrees of freedom
    ## Multiple R-squared:  0.9993, Adjusted R-squared:  0.9993 
    ## F-statistic: 8.085e+05 on 3 and 1704 DF,  p-value: < 2.2e-16

    predicted <- predict(fit,newdata = testData)

    ## Warning in predict.lm(fit, newdata = testData): prediction from a rank-
    ## deficient fit may be misleading

    predicted <- as_tibble(predicted)
    predicted$pos <- 1:nrow(predicted)
    testData$pos <- 1:nrow(testData)

    ggplot(predicted,aes(x = pos, y = value))+
        geom_line(color = "red")

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-9-1.png)

    ggplot(testData,aes(x = pos, y = Close))+
        geom_line(color = "blue")

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-9-2.png)

    ggplot()+
        geom_line(data = predicted,aes(x = pos, y = value),color = "red")+
        geom_line(data = testData,aes(x = pos, y = Close),color = "blue")

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-9-3.png)

По полученным графикам видно, что возможно некоторое переобучение, т.к.
график предсказанных значений довольно близок к реальному, а шумы не
очень сильные.

№2
==

В папке со скриптом на R находится папка Task с дополнительными
датасетами. Загрузим второй датасет и посмотрим график зависмости

    d <- read_csv("./Task/challenge_dataset.txt",col_names = F)

    head(d, n = 5)

    ## # A tibble: 5 x 2
    ##       X1      X2
    ##    <dbl>   <dbl>
    ## 1 6.1101 17.5920
    ## 2 5.5277  9.1302
    ## 3 8.5186 13.6620
    ## 4 7.0032 11.8540
    ## 5 5.8598  6.8233

    ggplot(d,aes(X1,X2, color = X1))+
        geom_point()+
        scale_color_gradient(low = "#0a5fd6",high = "#a900f2")

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-12-1.png)

Для этих данных построим простую линейную регрессию x2 от x1

    fit <- lm(X2 ~ X1, data = d)
    summary(fit)

    ## 
    ## Call:
    ## lm(formula = X2 ~ X1, data = d)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -5.8540 -1.9686 -0.5407  1.5360 14.1982 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) -3.89578    0.71948  -5.415 4.61e-07 ***
    ## X1           1.19303    0.07974  14.961  < 2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 3.024 on 95 degrees of freedom
    ## Multiple R-squared:  0.702,  Adjusted R-squared:  0.6989 
    ## F-statistic: 223.8 on 1 and 95 DF,  p-value: < 2.2e-16

    ggplot(d,aes(X1,X2, color = X1))+
        geom_point()+
        scale_color_gradient(low = "#0a5fd6",high = "#a900f2")+
        geom_smooth(method = lm,formula = y ~ x)

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-13-1.png)

Полученная модель может хорошо предсказать данные, для которых x1 &gt;
10, но в отрезке от 5 до 10 получился слишком большой разброс.

Т.к. зависимость очень x2 от x2 напоминает график функции корня и
логарифма, построим регрессию для этих двух преобразований

    fit <- lm(X2 ~ sqrt(X1), data = d)
    summary(fit)

    ## 
    ## Call:
    ## lm(formula = X2 ~ sqrt(X1), data = d)
    ## 
    ## Residuals:
    ##    Min     1Q Median     3Q    Max 
    ## -6.347 -2.034 -0.807  1.549 14.292 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) -16.0409     1.5280  -10.50   <2e-16 ***
    ## sqrt(X1)      7.8243     0.5349   14.63   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 3.072 on 95 degrees of freedom
    ## Multiple R-squared:  0.6925, Adjusted R-squared:  0.6893 
    ## F-statistic:   214 on 1 and 95 DF,  p-value: < 2.2e-16

    ggplot(d,aes(X1,X2, color = X1))+
        geom_point()+
        scale_color_gradient(low = "#0a5fd6",high = "#a900f2")+
        geom_smooth(method = lm,formula = y ~ sqrt(x))

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-14-1.png)

    fit <- lm(X2 ~ log(X1), data = d)
    summary(fit)

    ## 
    ## Call:
    ## lm(formula = X2 ~ log(X1), data = d)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -6.8544 -1.9875 -0.6243  1.7288 14.2967 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) -18.6458     1.8015  -10.35   <2e-16 ***
    ## log(X1)      12.1225     0.8774   13.82   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 3.193 on 95 degrees of freedom
    ## Multiple R-squared:  0.6677, Adjusted R-squared:  0.6642 
    ## F-statistic: 190.9 on 1 and 95 DF,  p-value: < 2.2e-16

    ggplot(d,aes(X1,X2, color = X1))+
        geom_point()+
        scale_color_gradient(low = "#0a5fd6",high = "#a900f2")+
        geom_smooth(method = lm, formula = y ~ log(x))

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-15-1.png)

Но все равно эти модели не очень хорошо описывают данные, можно и
получше. Вот так для примера будет строиться модель (не регрессия) для
этих данных по умолчанию (функция loess - LOcal regrESSion)

    ggplot(d,aes(X1,X2, color = X1))+
        geom_point()+
        scale_color_gradient(low = "#0a5fd6",high = "#a900f2")+
        geom_smooth()

    ## `geom_smooth()` using method = 'loess'

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-16-1.png)

У этой модели большие доверительные интервалы и покрывает она данные
лучше предыдущих.

№3
==

Загрузка данных

    d <- read_csv("./Task/global_co2.csv")

Будем предсказать параметр ‘Per capita’. Т.к. в этом параметре есть NA,
то необходимо сначала отчистить данные

    clean <- function(d, replace = 0){
        d %>% mutate_if(~ any(is.na(.)),~ ifelse(is.na(.),replace,.))
    }

    d <- clean(d,0)

Разделение данных на два множества: для обучения и для тестирования

    trainData <- d %>%
        slice(1:(3*nrow(d) %/% 4))
    testData <- d %>%
        slice(((3*nrow(d) %/% 4)+1):nrow(d))

    fit <- lm(`Per Capita` ~ .,data = d)
    summary(fit)

    ## 
    ## Call:
    ## lm(formula = `Per Capita` ~ ., data = d)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -0.23354 -0.04020  0.00289  0.02534  0.28754 
    ## 
    ## Coefficients:
    ##                 Estimate Std. Error t value Pr(>|t|)   
    ## (Intercept)    1.1337099  0.3776756   3.002  0.00295 **
    ## Year          -0.0006362  0.0002083  -3.054  0.00250 **
    ## Total          0.0049847  0.0109937   0.453  0.65064   
    ## `Gas Fuel`    -0.0028765  0.0109839  -0.262  0.79363   
    ## `Liquid Fuel` -0.0054650  0.0110010  -0.497  0.61978   
    ## `Solid Fuel`  -0.0047295  0.0109934  -0.430  0.66741   
    ## Cement        -0.0105909  0.0110457  -0.959  0.33856   
    ## `Gas Flaring`  0.0085927  0.0109775   0.783  0.43451   
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.08635 on 252 degrees of freedom
    ## Multiple R-squared:  0.9651, Adjusted R-squared:  0.9641 
    ## F-statistic: 995.8 on 7 and 252 DF,  p-value: < 2.2e-16

Видно, что в данной модели важны только два параметра Year и смещение.
Для полученной модели построим предсказанные значения.

    predict <- as_tibble(predict(fit,newdata = testData))
    predict$pos <- 1:nrow(predict)
    testData$pos <- 1:nrow(testData)
    ggplot()+
        geom_line(data = predict,aes(x = pos , y = value),color = "red")+
        geom_line(data = testData,aes(x = pos, y = `Per Capita`), color = "blue")

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-21-1.png)

В данной модели переобучения не видно. Так выглядит модель, если изменить
соотношение данных для обучения и тестирования как 1:1

    trainData <- d %>%
        slice(1:(nrow(d) %/% 2))
    testData <- d %>%
        slice(((nrow(d) %/% 2)+1):nrow(d))

    fit <- lm(`Per Capita` ~ .,data = d)
    summary(fit)

    ## 
    ## Call:
    ## lm(formula = `Per Capita` ~ ., data = d)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -0.23354 -0.04020  0.00289  0.02534  0.28754 
    ## 
    ## Coefficients:
    ##                 Estimate Std. Error t value Pr(>|t|)   
    ## (Intercept)    1.1337099  0.3776756   3.002  0.00295 **
    ## Year          -0.0006362  0.0002083  -3.054  0.00250 **
    ## Total          0.0049847  0.0109937   0.453  0.65064   
    ## `Gas Fuel`    -0.0028765  0.0109839  -0.262  0.79363   
    ## `Liquid Fuel` -0.0054650  0.0110010  -0.497  0.61978   
    ## `Solid Fuel`  -0.0047295  0.0109934  -0.430  0.66741   
    ## Cement        -0.0105909  0.0110457  -0.959  0.33856   
    ## `Gas Flaring`  0.0085927  0.0109775   0.783  0.43451   
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.08635 on 252 degrees of freedom
    ## Multiple R-squared:  0.9651, Adjusted R-squared:  0.9641 
    ## F-statistic: 995.8 on 7 and 252 DF,  p-value: < 2.2e-16

    predict <- as_tibble(predict(fit,newdata = testData))
    predict$pos <- 1:nrow(predict)
    testData$pos <- 1:nrow(testData)

    ggplot()+
        geom_line(data = predict,aes(x = pos , y = value),color = "red")+
        geom_line(data = testData,aes(x = pos, y = `Per Capita`), color = "blue")

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-24-1.png)

Также можно заполнять неизвестные значения другим способом. Например,
средним от известных значений

    d <- read_csv("./Task/global_co2.csv")

    d <- clean(d,mean(d$`Per Capita`,na.rm = T))

Разделение данных на два множества: для обучения и для тестирования

    trainData <- d %>%
        slice(1:(3*nrow(d) %/% 4))
    testData <- d %>%
        slice(((3*nrow(d) %/% 4)+1):nrow(d))

    fit <- lm(`Per Capita` ~ .,data = d)
    summary(fit)

    ## 
    ## Call:
    ## lm(formula = `Per Capita` ~ ., data = d)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -0.31886 -0.01070 -0.00461  0.02926  0.14335 
    ## 
    ## Coefficients:
    ##                 Estimate Std. Error t value Pr(>|t|)   
    ## (Intercept)    0.7648247  0.2867835   2.667  0.00815 **
    ## Year           0.0001668  0.0001582   1.055  0.29265   
    ## Total         -0.0011734  0.0083479  -0.141  0.88833   
    ## `Gas Fuel`    -0.0001460  0.0083405  -0.017  0.98605   
    ## `Liquid Fuel`  0.0017429  0.0083535   0.209  0.83489   
    ## `Solid Fuel`   0.0010040  0.0083477   0.120  0.90436   
    ## Cement         0.0048834  0.0083874   0.582  0.56093   
    ## `Gas Flaring` -0.0045245  0.0083357  -0.543  0.58776   
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.06557 on 252 degrees of freedom
    ## Multiple R-squared:  0.4341, Adjusted R-squared:  0.4184 
    ## F-statistic: 27.61 on 7 and 252 DF,  p-value: < 2.2e-16

    predict <- as_tibble(predict(fit,newdata = testData))
    predict$pos <- 1:nrow(predict)
    testData$pos <- 1:nrow(testData)
    ggplot()+
        geom_line(data = predict,aes(x = pos , y = value),color = "red")+
        geom_line(data = testData,aes(x = pos, y = `Per Capita`), color = "blue")

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-29-1.png)

В данной модели переобучения не видно. Так выглядит модель если изменить
соотношение данных для обучения и тестирования как 1:1

    trainData <- d %>%
        slice(1:(nrow(d) %/% 2))
    testData <- d %>%
        slice(((nrow(d) %/% 2)+1):nrow(d))

    fit <- lm(`Per Capita` ~ .,data = d)
    summary(fit)

    ## 
    ## Call:
    ## lm(formula = `Per Capita` ~ ., data = d)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -0.31886 -0.01070 -0.00461  0.02926  0.14335 
    ## 
    ## Coefficients:
    ##                 Estimate Std. Error t value Pr(>|t|)   
    ## (Intercept)    0.7648247  0.2867835   2.667  0.00815 **
    ## Year           0.0001668  0.0001582   1.055  0.29265   
    ## Total         -0.0011734  0.0083479  -0.141  0.88833   
    ## `Gas Fuel`    -0.0001460  0.0083405  -0.017  0.98605   
    ## `Liquid Fuel`  0.0017429  0.0083535   0.209  0.83489   
    ## `Solid Fuel`   0.0010040  0.0083477   0.120  0.90436   
    ## Cement         0.0048834  0.0083874   0.582  0.56093   
    ## `Gas Flaring` -0.0045245  0.0083357  -0.543  0.58776   
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.06557 on 252 degrees of freedom
    ## Multiple R-squared:  0.4341, Adjusted R-squared:  0.4184 
    ## F-statistic: 27.61 on 7 and 252 DF,  p-value: < 2.2e-16

    predict <- as_tibble(predict(fit,newdata = testData))
    predict$pos <- 1:nrow(predict)
    testData$pos <- 1:nrow(testData)

    ggplot()+
        geom_line(data = predict,aes(x = pos , y = value),color = "red")+
        geom_line(data = testData,aes(x = pos, y = `Per Capita`), color = "blue")

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-32-1.png)
