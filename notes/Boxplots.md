# 40 years of boxplots

11/2011  
http://vita.had.co.nz/papers/boxplots.html

## Introduction
- Introduzido: 1970; Difundido: 1977
- *Boxplots use robust summary statistics that are always located at actual data points, are quickly computable (originally by hand), and have no tuning parameters. They are particularly useful for comparing distributions across groups.*

## Turkey's Boxplot
- Problema: No visual display of group size -> Impossível determinar se as diferenças têm significância quando comparados lado-a-lado.
	- Possíveis soluções:
		- **Variable-width boxplot**: Largura varia de acordo com o número de integrantes do grupo.  
		- **Notched boxplot**: Mostra intervalos de confiança relativos à mediana. Maior amostragem geralmente resultará em maior confiança.
- Possíveis vieses de percepção:
	- *Whisker length is overestimated when whiskers are shorter than boxes and underestimated when whiskers are longer than boxes.*
	- *Box length is overestimated when boxes are shorter than whiskers and vice-versa.*
	- *Notched plots appear to suffer from similar problems*

## Richer displays of density
*One of the original constraints on the boxplot was that it was designed to be computed and drawn by hand. As every statistician now has a computer on their desk, this constraint can be relaxed, allowing variations of the boxplot that are substantially more complex.  These variations attempt to display more information about the distribution, maintaining the compact size of the boxplot, but bringing in the richer distributional summary of the histogram or density plot.  These plots can overcome problems in the original such as the failure to display multi-modality, or the excessive number of “outliers” when n is large.*
- **Vase plot**: Box is replaced with a symmetrical display of estimated density.
- **Violin plot**: Similar to vase, but display the density for all data points, not just the middle half.
- **Bean plot**: Adds a rug showing every value and a line that shows the mean.
- **Highest density region (HDR) boxplot**: Compromise between a boxplot and a density boxplot. Uses a density estimate but shows only two regions of highest density: the top 50% and 99%. These regions do not need to be contiguous and make it easy to spot multi-modality. The disadvantage of HDR boxplots is a less-sophisticated definition of extremes, making the outliers less useful for non-normal data.
- **Letter-value boxplot**: Designed to overcome the shortcomings of the boxplot for large data. For large datasets, the boxplot displays many outliers, and does not take advantage of the more reliable estimates of tail behavior. The letter-value boxplot extends the boxplot with additional letter-values apart from the median (M) and fourths (F): eighths (E), sixteenths (D), *et cetera*, until the estimation error becomes too large. Each additional letter-value is displayed with a slightly smaller box.  Because this display just adds extra letter values, it suffers from the same problems as the original boxplot, and multimodality is almost impossible to spot.

## Extentions to 2d
*Extending the boxplot to work in 2d is challenging because of the difficulty of defining order statistics, depth, and quantiles in 2d. There is no unique definition of rank in 2d dimensions, and hence the 2d analogues of medians, fourths and extremes becomes more complex (mathematically and computationally).*
- *Compared to 1d, it is less obvious that 2d boxplots provide significant advantages over contours of density estimates or heatmaps of binned counts.*
- **Rangefinder plot**: This generalization is simple, treating the two variables as independent and drawing two independent 1d boxplots.
- **Relplot**: Relaxes the assumption of independence by robustly fitting a bivariate Gaussian to the data and drawing 50% (corresponding to the box) and 99% (corresponding to the whiskers) confidence ellipses.
- **Quelplot**: Relaxes the assumption of normality by adding two degrees of asymmetry, accounting for residuals on both the major and minor axes of the ellipse.
- **Bagplot**: A 2d analog of the boxplot with a bag and fence containing the middle 50% of the data and a loop to separate outliers. The definitions of these regions follow naturally from the boxplot, using the half-space depth in lieu of rank.  The half-space depth, the smallest number of points contained within a half-plane of any orientation at a point, was informally defined early by Tukey, but an efficient algorithm for computing it was not available until over 20 years later.

## Discussion

*Boxplots were created to provide a succinct distributional summary that could easily be created by hand, and supported comparison across groups.  As computers have become more prevalent and more powerful, it has become easier to produce compact summaries that display more data.  This has led to an explosion of boxplot variation that stay true to the original goals to various extents, while supporting much richer display of the underlying distributions*
