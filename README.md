Загрузка необходимых пакетов

    require(tidyverse)
    require(Quandl)

№1
==

Загрузим необходимые данные

    d <- as_tibble(Quandl("WIKI/GOOGL"))

Построим простой график зависмости Close от Open

    ggplot(d,aes(x = Open, y = Close,color = Open))+
        geom_point(alpha = 0.5)+
        scale_colour_gradient(low = "orange", high = "red")

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-2-1.png)

Из этого графика можно заметить, что параметр Close сильно зависит от
Open Построим линейную регрессию и посмотрим на полученные результаты

    fit <- lm( Close ~ Open ,data = d)
    summary(fit)

    ## 
    ## Call:
    ## lm(formula = Close ~ Open, data = d)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -60.229  -3.890   0.122   4.412  50.939 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error  t value Pr(>|t|)    
    ## (Intercept) 0.173256   0.380095    0.456    0.649    
    ## Open        0.999359   0.000597 1673.959   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 7.877 on 3413 degrees of freedom
    ## Multiple R-squared:  0.9988, Adjusted R-squared:  0.9988 
    ## F-statistic: 2.802e+06 on 1 and 3413 DF,  p-value: < 2.2e-16

Видно что Close и Open соотносятся почти как 1 к 1 На графике это будет
ещё более заметно

    ggplot(d,aes(x = Open, y = Close,color = Open))+
        geom_point(alpha = 0.5)+
        scale_colour_gradient(low = "orange", high = "red")+
        geom_smooth(method = "lm", formula = y ~ x)

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-4-1.png)

Полученная модель получилась довольно слабой, т.к. очевидно что цены на
бирже редко когда сильно изменяются Того можно построить график цены во
время закрытия торгов

    cl <- d %>%
        select(Close) %>%
        arrange(Close)
    cl$days <- c(1:nrow(cl))
    ggplot(cl,aes(x = days, y = Close, color = Close))+
        geom_point(alpha = 0.2)+
        scale_color_gradient(low = "orange", high = "red")

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-5-1.png)

По этим даннм так же можно построить линейную регрессию Будем считать
что days - число дней с момента когда акции впервые повились на торгах

    fit <- lm(Close ~ days,data = cl)
    summary(fit)

    ## 
    ## Call:
    ## lm(formula = Close ~ days, data = cl)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -118.59  -50.62   -4.21   41.53  248.54 
    ## 
    ## Coefficients:
    ##              Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) 2.183e+02  2.077e+00   105.1   <2e-16 ***
    ## days        2.206e-01  1.053e-03   209.5   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 60.66 on 3413 degrees of freedom
    ## Multiple R-squared:  0.9279, Adjusted R-squared:  0.9278 
    ## F-statistic: 4.39e+04 on 1 and 3413 DF,  p-value: < 2.2e-16

    ggplot(cl,aes(x = days, y = Close, color = Close))+
        geom_point(alpha = 0.2)+
        scale_color_gradient(low = "orange", high = "red")+
        geom_smooth(method = "lm", formula = y ~ x)

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-6-1.png)

В целом эта модель будет по-полезнее, т.к. она может предсказать цену на
будущее, в то время как самая первая модель не имела особого смысла,
т.к. показывала что цена открытия примерно равна цене закрытия.

№2
==

В папке с скриптом на R находится папка Task с дополнительными
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

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-9-1.png)

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

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-10-1.png)

Полученная модель может примерно хорошо данные для которых x1 &gt; 10,
но в отрезке от 5 до 10 получился слишком большой разброс. Т.к.
зависимость очень x2 от x2 напоминает график функции корня и логарифма,
построим регрессию для этих двух преобразований

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

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-11-1.png)

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

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-12-1.png)

Но все равно эти модели не слишком хорошо описывают данные. Вот так для
примера будет строиться модель(не регрессия) для этих данных по
умолчанию

    ggplot(d,aes(X1,X2, color = X1))+
        geom_point()+
        scale_color_gradient(low = "#0a5fd6",high = "#a900f2")+
        geom_smooth()

    ## `geom_smooth()` using method = 'loess'

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-13-1.png)

У этой модели большие доверительные интервалы и покрывает она данные
лучше предыдущих.

№3
==

    d <- read_csv("./Task/global_co2.csv")

Попытаемся построить регрессию от поля “Solid Fuel” для “Per Capita”

    fit <- lm(d$'Per Capita' ~ d$'Solid Fuel',data = d)
    summary(fit)

    ## 
    ## Call:
    ## lm(formula = d$"Per Capita" ~ d$"Solid Fuel", data = d)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -0.22339 -0.06968 -0.01502  0.03983  0.21281 
    ## 
    ## Coefficients:
    ##                 Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)    6.460e-01  4.341e-02  14.883  < 2e-16 ***
    ## d$"Solid Fuel" 2.031e-04  2.041e-05   9.954 3.05e-14 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.11 on 59 degrees of freedom
    ##   (199 observations deleted due to missingness)
    ## Multiple R-squared:  0.6268, Adjusted R-squared:  0.6205 
    ## F-statistic: 99.09 on 1 and 59 DF,  p-value: 3.051e-14

    ggplot(d,aes(x = d$'Solid Fuel', y = d$'Per Capita', color = d$'Solid Fuel'))+
        geom_point()+
        labs(x = "Solid Fuel", y = "Per Capita", color = "Solid Fuel")+
        scale_color_gradient(low = "#570777", high = "#c60590")+
        geom_smooth(method = lm,formula = y ~ x)

    ## Warning: Removed 199 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 199 rows containing missing values (geom_point).

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-15-1.png)

Но в этой модели есть много(200) пропущенных значений “Per Capita”. Из
простых логических соображений поле Per Capita не может быть
отрицательным, так что можно попробовать заменить все неизвестные
значение на 0

    newData <- d  %>%
        mutate_if(~ any(is.na(.)),~ ifelse(is.na(.),0,.))
    fit <- lm(newData$'Per Capita' ~ newData$'Solid Fuel',data = newData)
    summary(fit)

    ## 
    ## Call:
    ## lm(formula = newData$"Per Capita" ~ newData$"Solid Fuel", data = newData)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -0.44402 -0.09863  0.05888  0.06841  0.50572 
    ## 
    ## Coefficients:
    ##                        Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)          -7.018e-02  1.589e-02  -4.415 1.48e-05 ***
    ## newData$"Solid Fuel"  4.709e-04  1.447e-05  32.538  < 2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.2022 on 258 degrees of freedom
    ## Multiple R-squared:  0.8041, Adjusted R-squared:  0.8033 
    ## F-statistic:  1059 on 1 and 258 DF,  p-value: < 2.2e-16

    ggplot(d,aes(x = newData$'Solid Fuel', y = newData$'Per Capita', color = newData$'Solid Fuel'))+
        geom_point()+
        labs(x = "Solid Fuel", y = "Per Capita", color = "Solid Fuel")+
        scale_color_gradient(low = "#570777", high = "#c60590")+
        geom_smooth(method = lm,formula = y ~ x)

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-16-1.png)

Получилось какая то ужасная кривая, которая очень плохо, теперь
попробуем заменять на средние значение от известных величин

    newData <- d  %>%
        mutate_if(~ any(is.na(.)),~ ifelse(is.na(.),mean(.,na.rm = T),.))
    fit <- lm(newData$'Per Capita' ~ newData$'Solid Fuel',data = newData)
    summary(fit)

    ## 
    ## Call:
    ## lm(formula = newData$"Per Capita" ~ newData$"Solid Fuel", data = newData)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -0.42672 -0.00005  0.01865  0.02025  0.17944 
    ## 
    ## Coefficients:
    ##                       Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)          1.034e+00  6.446e-03 160.460  < 2e-16 ***
    ## newData$"Solid Fuel" 3.025e-05  5.869e-06   5.154 5.08e-07 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.08202 on 258 degrees of freedom
    ## Multiple R-squared:  0.09334,    Adjusted R-squared:  0.08982 
    ## F-statistic: 26.56 on 1 and 258 DF,  p-value: 5.083e-07

    ggplot(d,aes(x = newData$'Solid Fuel', y = newData$'Per Capita', color = newData$'Solid Fuel'))+
        geom_point()+
        labs(x = "Solid Fuel", y = "Per Capita", color = "Solid Fuel")+
        scale_color_gradient(low = "#570777", high = "#c60590")+
        geom_smooth(method = lm,formula = y ~ x)

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-17-1.png)

Можно вместо неизвестных значений выставлять значения, который точно не
могут подходить,например, -1

    newData <- d  %>%
        mutate_if(~ any(is.na(.)),~ ifelse(is.na(.),-1,.))
    fit <- lm(newData$'Per Capita' ~ newData$'Solid Fuel',data = newData)
    summary(fit)

    ## 
    ## Call:
    ## lm(formula = newData$"Per Capita" ~ newData$"Solid Fuel", data = newData)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -0.96675 -0.20121  0.09604  0.11470  0.89244 
    ## 
    ## Coefficients:
    ##                        Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)          -1.117e+00  3.275e-02  -34.12   <2e-16 ***
    ## newData$"Solid Fuel"  8.886e-04  2.982e-05   29.80   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.4168 on 258 degrees of freedom
    ## Multiple R-squared:  0.7749, Adjusted R-squared:  0.774 
    ## F-statistic: 887.9 on 1 and 258 DF,  p-value: < 2.2e-16

    ggplot(d,aes(x = newData$'Solid Fuel', y = newData$'Per Capita', color = newData$'Solid Fuel'))+
        geom_point()+
        labs(x = "Solid Fuel", y = "Per Capita", color = "Solid Fuel")+
        scale_color_gradient(low = "#570777", high = "#c60590")+
        geom_smooth(method = lm,formula = y ~ x)

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-18-1.png)

Полученные модели с заменой на какое-нибудь значение получаются не такие
уж и эффективные. Но в целом можно начать перебирать значения и выбрать
наиболее удобное(почему бы и не 0.5?)

    newData <- d  %>%
        mutate_if(~ any(is.na(.)),~ ifelse(is.na(.),0.5,.))
    fit <- lm(newData$'Per Capita' ~ newData$'Solid Fuel',data = newData)
    summary(fit)

    ## 
    ## Call:
    ## lm(formula = newData$"Per Capita" ~ newData$"Solid Fuel", data = newData)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -0.23952 -0.05211  0.03532  0.04527  0.31236 
    ## 
    ## Coefficients:
    ##                       Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)          4.534e-01  8.298e-03   54.64   <2e-16 ***
    ## newData$"Solid Fuel" 2.620e-04  7.556e-06   34.67   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.1056 on 258 degrees of freedom
    ## Multiple R-squared:  0.8233, Adjusted R-squared:  0.8226 
    ## F-statistic:  1202 on 1 and 258 DF,  p-value: < 2.2e-16

    ggplot(d,aes(x = newData$'Solid Fuel', y = newData$'Per Capita', color = newData$'Solid Fuel'))+
        geom_point()+
        labs(x = "Solid Fuel", y = "Per Capita", color = "Solid Fuel")+
        scale_color_gradient(low = "#570777", high = "#c60590")+
        geom_smooth(method = lm,formula = y ~ x)

![](report%5Bexported%5D_files/figure-markdown_strict/unnamed-chunk-19-1.png)
