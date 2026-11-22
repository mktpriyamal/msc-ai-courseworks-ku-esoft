# Master Seaborn: Complete Course

Nov 22, 2026 · @Thisara Priyamal

This 6-week course takes you from your first `sns.scatterplot()` to polished, multi-panel statistical graphics, covering every major Seaborn function with a use case for each.

## Course overview

**Who it is for:** analysts, data scientists and students who know basic Python and pandas. Seaborn is a high-level library built on Matplotlib that turns tidy DataFrames into statistical charts in one line.

**What you will be able to do:** pick the right statistical chart, draw it from a DataFrame, split it by category into panels, control colour and style, combine it with Matplotlib for fine control, and use the newer `seaborn.objects` interface.

**Prerequisites:** Python, pandas basics and a little Matplotlib (the companion Matplotlib course in this series covers it).

| Week | Modules | Milestone |
| --- | --- | --- |
| 1 | 1 to 2 | Understand tidy data and plot function families; relational plots |
| 2 | 3 to 4 | Distribution and categorical plots |
| 3 | 5 to 6 | Regression and matrix plots |
| 4 | 7 to 8 | Grids of plots; themes and palettes |
| 5 | 9 to 10 | Matplotlib integration; the objects interface |
| 6 | 11 to 12 | EDA workflow; two capstone projects |

```python
import seaborn as sns
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np

print(sns.__version__)
sns.set_theme()          # apply Seaborn's default look
```

## Module 1: Setup, philosophy, tidy data and function families

Seaborn's design rests on two ideas: data in tidy (long) form, and a small set of function families with a consistent API.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Installation | `pip install seaborn`; depends on NumPy, pandas, Matplotlib | `pip`, `conda` | Setup |
| Example datasets | Bundled practice data (downloaded on first use) | `sns.load_dataset('tips'/'penguins'/'iris'/'flights'/'titanic'/'diamonds'/'planets'/'mpg')`, `sns.get_dataset_names()` | Learning and demos |
| Tidy (long) data | One row per observation, one column per variable; ideal for mapping columns to visual properties | `df.melt`, `df.pivot` | Preparing data |
| Wide-form data | Also accepted for simple plots | `sns.lineplot(data=wide_df)` | Quick plots |
| The `data, x, y, hue, size, style` pattern | Pass a DataFrame and column names; semantic mappings add dimensions | `sns.scatterplot(data=df, x='a', y='b', hue='c')` | Multivariate plots in one call |
| Axes-level functions | Draw onto one Matplotlib `Axes`; accept `ax=` | `scatterplot`, `histplot`, `boxplot`, `heatmap` | Composing your own subplots |
| Figure-level functions | Create a whole figure, manage facets and legends outside the axes | `relplot`, `displot`, `catplot`, `lmplot`, `pairplot`, `jointplot` | Splitting by category with `col=`, `row=` |
| Choosing between them | Axes-level for custom layouts; figure-level for faceting | Returns `Axes` vs `FacetGrid` | Design decision |
| Statistical aggregation | Many plots compute means, confidence intervals, densities for you | `estimator=`, `errorbar=`, `stat=` | Honest summaries |
| Missing data | NaN values are dropped from each plot | Clean first with pandas | Avoiding surprises |

```python
tips = sns.load_dataset('tips')
tips.head()

# axes-level: draws on one Axes you control
fig, ax = plt.subplots(figsize=(6, 4))
sns.scatterplot(data=tips, x='total_bill', y='tip', hue='time', ax=ax)

# figure-level: creates the figure, splits by column
g = sns.relplot(data=tips, x='total_bill', y='tip',
                hue='smoker', col='time', height=3.5)
g.set_axis_labels('Bill ($)', 'Tip ($)')

# wide to long for plotting
long = flights_wide.melt(ignore_index=False, var_name='month', value_name='n')
```

**Exercises**

1. Load three bundled datasets and print their shapes, dtypes and first rows.
2. Draw the same scatter with an axes-level function on your own subplot and with a figure-level function split by `col`.
3. Convert a wide spreadsheet-style table of monthly sales into long form and plot it with `hue`.

## Module 2: Relational plots

Show how variables relate: scatter for individual observations, lines for trends and aggregated change.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| `scatterplot` | Points for two numeric variables; extra variables map to colour, size and marker | `sns.scatterplot(data=, x=, y=, hue=, size=, style=, alpha=, palette=, sizes=)` | Relationships, clusters |
| `lineplot` | Lines with automatic aggregation and a confidence band when x has repeats | `sns.lineplot(data=, x=, y=, hue=, style=, units=, estimator='mean', errorbar=('ci', 95), markers=True)` | Trends over time with uncertainty |
| `relplot` | Figure-level wrapper with faceting | `sns.relplot(kind='scatter'/'line', col=, row=, col_wrap=, height=, aspect=)` | Small multiples |
| Semantic mappings | `hue` (colour), `size`, `style` (marker or dash) encode extra variables | `hue_order=`, `size_norm=`, `palette=` | Multi-variable views |
| Aggregation control | Choose summary and uncertainty | `estimator='median'`, `errorbar='sd'/'se'/('pi', 90)/None`, `n_boot=` | Honest uncertainty |
| Individual series | Plot each unit separately without aggregation | `units='subject', estimator=None` | Per-person trajectories |
| Ordering | Sort x or keep data order | `sort=False` | Paths and trajectories |
| Dates | Datetime x-axes | Combine with `fig.autofmt_xdate()` | Time series |
| Reference marks | Add to the underlying Axes | `ax.axhline`, `ax.axvline` | Thresholds |
| Legend control | Placement and style | `legend='full'/'brief'/False`, `sns.move_legend` | Clean legends |

```python
tips = sns.load_dataset('tips')
flights = sns.load_dataset('flights')

sns.scatterplot(data=tips, x='total_bill', y='tip',
                hue='day', size='size', style='smoker', alpha=.8)

# mean passengers per year with 95% confidence band, one line per month group
sns.lineplot(data=flights, x='year', y='passengers', hue='month')

# faceted line chart
g = sns.relplot(data=flights, x='year', y='passengers', col='month',
                col_wrap=4, kind='line', height=2, aspect=1.3)

# per-subject trajectories
sns.lineplot(data=df, x='week', y='score', units='subject',
             estimator=None, alpha=.4, color='gray')
```

**Exercises**

1. Plot tip vs bill with colour for day, size for party size and marker for smoker; decide which encodings help and which clutter.
2. Draw yearly passenger trends with a median line and a 90% prediction interval.
3. Create a grid of line charts, one per category, with shared axes and a single title.

## Module 3: Distribution plots

Look at how values are spread before summarising them.

| Plot | Function | Shows | Key options |
| --- | --- | --- | --- |
| Histogram | `sns.histplot` | Counts in bins | `bins=`, `binwidth=`, `stat='count'/'density'/'probability'`, `kde=True`, `cumulative=True`, `element='step'`, `multiple='layer'/'stack'/'dodge'/'fill'` |
| Kernel density | `sns.kdeplot` | Smooth density estimate | `bw_adjust=`, `fill=True`, `common_norm=`, `cut=`, `levels=` for 2D contours |
| ECDF | `sns.ecdfplot` | Cumulative share, no binning choices | `stat='proportion'`, `complementary=True` |
| Rug | `sns.rugplot` | Individual observations as ticks | `height=`, `expand_margins` |
| Bivariate distributions | `histplot(x=, y=)`, `kdeplot(x=, y=)` | Joint density | `thresh=`, `levels=`, `cbar=True` |
| `displot` | Figure-level; faceting | `kind='hist'/'kde'/'ecdf'`, `col=`, `row=`, `rug=True` | Distributions per group |
| `jointplot` | Joint plus marginals | `kind='scatter'/'hist'/'kde'/'hex'/'reg'/'resid'`, `marginal_kws=` | Two-variable exploration |
| Comparing groups | Overlay or split | `hue=`, `multiple=`, `common_norm=False` | Group comparison |
| Log scales | Skewed data | `log_scale=True` | Income, prices |
| Weighted data | Counts or weights | `weights=` | Aggregated tables |
| Choosing bins and bandwidth | Defaults can mislead; try several | `bins='auto'`, `bw_adjust` | Avoiding false shapes |

```python
penguins = sns.load_dataset('penguins')

sns.histplot(data=penguins, x='flipper_length_mm', hue='species',
             multiple='stack', binwidth=3)

sns.kdeplot(data=penguins, x='body_mass_g', hue='species',
            fill=True, common_norm=False, alpha=.4)

sns.ecdfplot(data=penguins, x='bill_length_mm', hue='species')

g = sns.displot(data=penguins, x='bill_length_mm', col='island',
                hue='species', kind='kde', fill=True, height=3)

sns.jointplot(data=penguins, x='bill_length_mm', y='bill_depth_mm',
              hue='species', kind='kde')
```

**Exercises**

1. Compare the distribution of body mass for three species using histograms, KDEs and ECDFs; say which view is clearest and why.
2. Show how the shape of a histogram changes with 5, 20 and 80 bins.
3. Make a log-scaled histogram of a right-skewed variable such as income.
4. Build a `jointplot` with hexagonal bins for a large dataset.

## Module 4: Categorical plots

Compare a numeric variable across categories, from raw points to summaries.

| Plot | Function | Shows | Best for |
| --- | --- | --- | --- |
| Strip | `sns.stripplot` | Every observation as a jittered point | Small data |
| Swarm | `sns.swarmplot` | Points without overlap | Up to a few hundred points per group |
| Box | `sns.boxplot` | Median, quartiles, whiskers, outliers | Group comparison |
| Boxen (letter-value) | `sns.boxenplot` | More quantiles than a box | Large samples |
| Violin | `sns.violinplot` | Density shape with quartiles | Distribution shape per group |
| Bar | `sns.barplot` | Mean (or other estimator) with error bar | Summaries; start at zero |
| Point | `sns.pointplot` | Estimates connected by lines, with error bars | Interaction of two categories |
| Count | `sns.countplot` | Number of observations per category | Frequencies |
| `catplot` | Figure-level for all kinds with faceting | `kind='strip'/'swarm'/'box'/'boxen'/'violin'/'bar'/'point'/'count'`, `col=`, `row=` | Panels per group |

**Common options:** `order=` and `hue_order=` set category order; `hue=` adds a second category; `dodge=` separates hue groups; `orient='h'` flips direction; `estimator=` and `errorbar=` control bar and point summaries; `split=True` makes half-violins; `inner='quartile'/'box'/'point'/None`; `saturation=`, `width=`, `fill=`; `native_scale=True` for numeric categories.

**Layering:** combine a violin or box with a strip or swarm of the raw points so readers see both summary and data.

```python
tips = sns.load_dataset('tips')
titanic = sns.load_dataset('titanic')

fig, axs = plt.subplots(1, 3, figsize=(13, 4), constrained_layout=True)

sns.boxplot(data=tips, x='day', y='total_bill', hue='sex', ax=axs[0])

sns.violinplot(data=tips, x='day', y='total_bill', inner=None, ax=axs[1], color='.9')
sns.stripplot(data=tips, x='day', y='total_bill', size=3, color='k', ax=axs[1])

sns.barplot(data=titanic, x='class', y='survived', hue='sex',
            errorbar=('ci', 95), ax=axs[2])
axs[2].set_ylabel('survival rate')

sns.catplot(data=tips, x='day', y='tip', col='time', kind='box', height=3.5)
sns.countplot(data=titanic, y='deck', order=titanic['deck'].value_counts().index)
```

**Exercises**

1. Show tip percentage by day with a box plot overlaid with a swarm plot of the raw data.
2. Plot survival rate by class and sex with error bars, then explain what the bars represent.
3. Order categories by their median value rather than alphabetically.
4. Use `catplot` to build a grid of violins, one column per meal time.

## Module 5: Regression and model-fit plots

Seaborn can fit and draw simple models with confidence intervals. It is for exploration, not a replacement for a proper statistical model.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| `regplot` | Scatter plus fitted line (axes-level) | `sns.regplot(data=, x=, y=, order=, logistic=, lowess=, robust=, ci=95, scatter_kws=, line_kws=)` | One relationship |
| `lmplot` | Figure-level regression with `hue`, `col`, `row` | `sns.lmplot(data=, x=, y=, hue=, col=, height=, aspect=)` | Compare slopes across groups |
| Polynomial fits | Curved trends | `order=2` | Nonlinear trends |
| Logistic regression | Binary outcomes | `logistic=True` (requires statsmodels) | Probability vs variable |
| Robust regression | Down-weight outliers | `robust=True` (requires statsmodels) | Outlier-heavy data |
| LOWESS smoothing | Local trend, no assumptions | `lowess=True` (requires statsmodels) | Exploratory trend |
| Discretised x | Bin or jitter integer x | `x_bins=`, `x_jitter=`, `x_estimator=np.mean` | Small-integer predictors |
| Residual plots | Check model assumptions | `sns.residplot(data=, x=, y=, lowess=True)` | Diagnostics |
| Confidence bands | Uncertainty of the fit | `ci=95`, `n_boot=` | Interpretation |
| Marginal fits | Regression with marginal histograms | `sns.jointplot(kind='reg')` | Joint view |
| Limits of these plots | No formulas, no p-values, no multiple predictors | Use `statsmodels` or `scikit-learn` for modelling | Know when to stop |

```python
tips = sns.load_dataset('tips')
mpg = sns.load_dataset('mpg')

sns.regplot(data=tips, x='total_bill', y='tip', scatter_kws={'alpha': .4})

sns.lmplot(data=tips, x='total_bill', y='tip', hue='smoker', col='time',
           height=3.5)

sns.regplot(data=mpg, x='horsepower', y='mpg', order=2, ci=90)

# residuals should look like random noise around zero
sns.residplot(data=mpg.dropna(), x='horsepower', y='mpg', lowess=True,
              line_kws={'color': 'red'})
```

**Exercises**

1. Fit linear, quadratic and LOWESS lines to the same data and decide which fits best by looking at residuals.
2. Draw separate regression lines for each category and compare their slopes.
3. Use `jointplot(kind='reg')` to summarise a relationship and its marginal distributions.

## Module 6: Matrix plots

Visualise rectangular data: correlations, pivot tables, confusion matrices and clustered patterns.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| `heatmap` | Colour-coded matrix with optional cell labels | `sns.heatmap(data, annot=True, fmt='.2f', cmap=, vmin=, vmax=, center=, linewidths=, cbar_kws=, square=, mask=)` | Correlation, pivot tables |
| Annotations | Print values in cells | `annot=True`, `fmt='d'`, `annot_kws={'size': 8}` | Exact values |
| Diverging data | Centre colour at zero | `cmap='vlag'/'coolwarm'`, `center=0` | Correlations |
| Masking | Hide half of a symmetric matrix | `mask=np.triu(np.ones_like(corr, dtype=bool))` | Cleaner correlation plots |
| Pivot then plot | Reshape long data first | `df.pivot_table(index=, columns=, values=)` | Year by month grids |
| `clustermap` | Heatmap with hierarchical clustering of rows and columns | `sns.clustermap(data, method='average', metric='euclidean', standard_scale=1, z_score=0, row_colors=, col_cluster=)` | Find groups of similar rows or columns |
| Scaling | Make columns comparable | `standard_scale=`, `z_score=` | Mixed-unit data |
| Annotation colours | Side bars marking groups | `row_colors=`, `col_colors=` | Group labels on clusters |
| Confusion matrix | Counts of predicted vs actual | `heatmap(cm, annot=True, fmt='d', xticklabels=, yticklabels=)` | Classification results |
| Large matrices | Hide labels, use `rasterized` | `xticklabels=False`, `rasterized=True` | Many rows |

```python
flights = sns.load_dataset('flights').pivot(index='month', columns='year',
                                            values='passengers')
sns.heatmap(flights, cmap='rocket_r', linewidths=.3, annot=True, fmt='d')

corr = sns.load_dataset('penguins').select_dtypes('number').corr()
mask = np.triu(np.ones_like(corr, dtype=bool))
sns.heatmap(corr, mask=mask, annot=True, fmt='.2f',
            cmap='vlag', center=0, vmin=-1, vmax=1, square=True)

sns.clustermap(iris.drop(columns='species'), standard_scale=1,
               cmap='mako', figsize=(6, 6))

sns.heatmap(cm, annot=True, fmt='d', cmap='Blues', cbar=False)
```

**Exercises**

1. Plot a year-by-month heatmap of flight passengers and describe the seasonal pattern.
2. Draw a lower-triangle correlation heatmap with a diverging colormap centred at zero.
3. Use `clustermap` on a standardised dataset and interpret the dendrograms.
4. Visualise a classifier's confusion matrix with counts and row-normalised percentages.

## Module 7: Multi-plot grids

Grids draw the same plot across subsets of the data or across pairs of variables.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| `FacetGrid` | Split data by `row`, `col`, `hue`; map any plotting function | `g = sns.FacetGrid(data, col=, row=, hue=, col_wrap=, height=, aspect=, sharex=, sharey=, margin_titles=)`, `g.map_dataframe(sns.histplot, x='a')`, `g.add_legend()` | Small multiples with any plot |
| FacetGrid helpers | Tidy up panels | `g.set_titles('{col_name}')`, `g.set_axis_labels`, `g.set(xlim=, ylim=)`, `g.refline`, `g.tight_layout`, `g.axes_dict`, `g.figure` | Consistent panels |
| `PairGrid` | Grid of every variable pair, with different plots on and off the diagonal | `g = sns.PairGrid(df, hue=, vars=, diag_sharey=)`, `g.map_upper`, `g.map_lower`, `g.map_diag`, `g.map_offdiag` | Custom pair plots |
| `pairplot` | One-line pair grid | `sns.pairplot(df, hue=, kind='scatter'/'kde'/'reg'/'hist', diag_kind='hist'/'kde', corner=True, vars=, plot_kws=)` | First look at many variables |
| `JointGrid` | Joint plot with full control over centre and margins | `g = sns.JointGrid(data=, x=, y=)`, `g.plot_joint(sns.scatterplot)`, `g.plot_marginals(sns.histplot)` | Custom joint views |
| Figure-level wrappers | `relplot`, `displot`, `catplot`, `lmplot` are built on grids | `col=`, `row=`, `col_wrap=` | Faceting in one call |
| Legends in grids | Placed outside axes | `g.add_legend()`, `sns.move_legend(g, ...)` | Clean layout |
| Accessing Matplotlib | Reach underlying axes | `g.axes`, `g.axes_dict`, `g.figure`, `g.ax_joint`, `g.ax_marg_x` | Fine tuning |
| Many panels | Control size and wrapping | `height=`, `aspect=`, `col_wrap=`, `corner=True` | Readable output |

```python
penguins = sns.load_dataset('penguins')

sns.pairplot(penguins, hue='species', corner=True, diag_kind='kde')

g = sns.FacetGrid(penguins, col='island', row='sex', hue='species',
                  height=2.5, margin_titles=True)
g.map_dataframe(sns.scatterplot, x='bill_length_mm', y='bill_depth_mm')
g.add_legend()

g = sns.PairGrid(penguins, vars=['bill_length_mm', 'body_mass_g'], hue='species')
g.map_upper(sns.scatterplot)
g.map_lower(sns.kdeplot)
g.map_diag(sns.histplot)

g = sns.JointGrid(data=penguins, x='bill_length_mm', y='flipper_length_mm')
g.plot_joint(sns.scatterplot, s=30, alpha=.6)
g.plot_marginals(sns.histplot, kde=True)
```

**Exercises**

1. Draw a pairplot of four numeric columns coloured by category and note which pairs separate groups best.
2. Build a `FacetGrid` with a custom function that draws a histogram plus a vertical mean line in each panel.
3. Create a `PairGrid` with scatter on the lower triangle, KDE on the upper and histograms on the diagonal.
4. Make a `JointGrid` with a hexbin centre and rugged marginals.

## Module 8: Themes, styles, contexts and colour palettes

Seaborn separates the look into themes (background and grid), context (scale for the medium) and palettes (colour).

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| One-call theming | Set style, context, palette and font together | `sns.set_theme(style='whitegrid', context='talk', palette='deep', font='sans-serif', font_scale=1.1, rc={...})` | Project defaults |
| Styles | Background and grid look | `'darkgrid'`, `'whitegrid'`, `'dark'`, `'white'`, `'ticks'` | Match audience and medium |
| Temporary styles | Scoped change | `with sns.axes_style('white'):` | One figure only |
| Contexts | Scale labels and lines | `'paper'`, `'notebook'`, `'talk'`, `'poster'`; `sns.plotting_context` | Slides vs papers |
| Despining | Remove top and right borders | `sns.despine(offset=, trim=True, left=)` | Minimal look |
| Qualitative palettes | Distinct colours for categories | `'deep'`, `'muted'`, `'pastel'`, `'bright'`, `'dark'`, `'colorblind'`, `'Set2'`, `'tab10'` | Hue categories |
| Sequential palettes | Ordered low-to-high data | `'rocket'`, `'mako'`, `'flare'`, `'crest'`, `'viridis'`, `'Blues'`, `sns.light_palette`, `sns.dark_palette` | Numeric hue, heatmaps |
| Diverging palettes | Data with a meaningful centre | `'vlag'`, `'icefire'`, `'coolwarm'`, `sns.diverging_palette(h_neg, h_pos, s, l, center)` | Correlations, differences |
| Custom palettes | Build from colours | `sns.color_palette(['#4C78A8', '#F58518'])`, `sns.cubehelix_palette`, `sns.blend_palette` | Brand colours |
| Palette tools | Preview and use | `sns.palplot` (older), `sns.color_palette(...)` shown in notebooks, `as_cmap=True`, `n_colors=` | Choosing colours |
| Colourblind safety | Avoid red-green encodings | `palette='colorblind'` | Accessibility |
| Saturation and alpha | Soften large fills | `saturation=`, `alpha=` | Visual balance |
| Dictionary palettes | Fixed colour per category | `palette={'A': 'tab:blue', 'B': 'tab:red'}` | Consistency across charts |
| Resetting | Return to Matplotlib defaults | `sns.reset_defaults()`, `sns.reset_orig()` | Mixed workflows |

```python
sns.set_theme(style='ticks', context='notebook', palette='colorblind',
              font_scale=1.1)

tips = sns.load_dataset('tips')
sns.scatterplot(data=tips, x='total_bill', y='tip', hue='size',
                palette='crest')
sns.despine()

# consistent colours for the same categories across every chart
species_colors = {'Adelie': '#4C78A8', 'Chinstrap': '#F58518', 'Gentoo': '#54A24B'}
sns.histplot(data=penguins, x='body_mass_g', hue='species', palette=species_colors)

div = sns.diverging_palette(240, 10, as_cmap=True, center='light')
sns.heatmap(corr, cmap=div, center=0)

with sns.plotting_context('poster'):
    sns.lineplot(data=flights, x='year', y='passengers')
```

**Exercises**

1. Draw one chart in all five styles and choose the best for a printed paper and for a presentation.
2. Define a company palette dictionary and reuse it so each category keeps its colour in three different charts.
3. Make a colourblind-safe version of a red/green chart and test it in grayscale.

## Module 9: Customisation and Matplotlib integration

Seaborn returns Matplotlib objects, so anything Seaborn does not expose is one Matplotlib call away.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Axes-level return | Returns an `Axes` | `ax = sns.boxplot(...)`, `ax.set_title(...)` | Fine tuning |
| Figure-level return | Returns a grid object with `.figure` and `.axes` | `g = sns.relplot(...)`, `g.figure.suptitle(...)`, `g.set_axis_labels(...)`, `g.set_titles(...)` | Facet customisation |
| Subplots with Seaborn | Draw several axes-level plots on one figure | `fig, axs = plt.subplots(1, 3)`, pass `ax=axs[i]` | Custom dashboards |
| Titles and labels | Describe the chart | `ax.set(title=, xlabel=, ylabel=)` | Every chart |
| Tick labels | Rotate, format, relabel | `ax.tick_params(axis='x', labelrotation=45)`, `ax.set_xticks`, `plt.setp` | Long category names |
| Axis limits and scales | Zoom, log | `ax.set(xlim=, yscale='log')`, `log_scale=True` | Skewed data |
| Legends | Reposition and restyle | `sns.move_legend(ax, 'upper left', bbox_to_anchor=(1, 1), title=)`, `ax.legend(...)` | Legends outside the plot |
| Reference lines | Thresholds and means | `ax.axhline`, `ax.axvline`, `g.refline(x=, y=)` | Targets |
| Annotations | Callouts and bar labels | `ax.annotate`, `ax.text`, `ax.bar_label(ax.containers[0])` | Show values |
| Size and aspect | Figure dimensions | `plt.figure(figsize=)`, `height=` and `aspect=` for figure-level functions | Fit the medium |
| Layout | Prevent overlap | `constrained_layout=True`, `g.tight_layout()`, `plt.tight_layout()` | Clean spacing |
| Saving | Export | `fig.savefig('f.png', dpi=300, bbox_inches='tight')`, `g.savefig('g.png')` | Reports, papers |
| Overlaying layers | Combine plots on one axes | Call two Seaborn functions with the same `ax` | Violin plus swarm |
| Using raw Matplotlib | Anything not built in | `ax.fill_between`, `ax.errorbar`, patches | Special visuals |

```python
sns.set_theme(style='whitegrid')
tips = sns.load_dataset('tips')

fig, ax = plt.subplots(figsize=(7, 4), constrained_layout=True)
sns.barplot(data=tips, x='day', y='total_bill', hue='sex', ax=ax)

for c in ax.containers:
    ax.bar_label(c, fmt='%.0f', padding=2)

ax.axhline(tips['total_bill'].mean(), ls='--', color='gray')
ax.set(title='Average bill by day', xlabel='', ylabel='USD')
sns.move_legend(ax, 'upper left', bbox_to_anchor=(1, 1), title='Sex')
sns.despine(ax=ax)
fig.savefig('bill.png', dpi=300, bbox_inches='tight')

g = sns.catplot(data=tips, x='day', y='tip', col='time', kind='box', height=3.2)
g.set_axis_labels('', 'Tip ($)')
g.figure.suptitle('Tips by day and meal', y=1.04)
```

**Exercises**

1. Build a 1x3 figure mixing a boxplot, a histogram and a heatmap, each on its own axes with a shared style.
2. Add value labels to every bar and move the legend outside the plot.
3. Customise a `catplot` grid so each panel has a descriptive title and a shared y-axis label.

## Module 10: Statistical estimation and the seaborn.objects interface

Since version 0.12, Seaborn includes a declarative, grammar-of-graphics style interface (`seaborn.objects`). You build a plot by combining data mappings, marks, statistical transforms and scales.

**Part A: Estimation and uncertainty (classic API)**

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Error bars | Choose what the interval means | `errorbar='ci'`, `('ci', 95)`, `'sd'`, `'se'`, `('pi', 95)`, or a custom function; replaces the older `ci=` and `sd` arguments in recent versions | Honest uncertainty |
| Bootstrapping | Resampled confidence intervals | `n_boot=`, `seed=` | Reproducible intervals |
| Estimators | Summary statistic | `estimator='mean'/'median'/'sum'/np.std` or a callable | Medians for skewed data |
| Reading intervals | A confidence interval of the mean is not the spread of the data | Use `'sd'` or `'pi'` for spread | Avoiding misinterpretation |
| Weights and normalisation | Weighted histograms and shares | `weights=`, `stat='probability'`, `common_norm=` | Group comparisons |

**Part B: The objects interface**

| Concept | Description | Example |
| --- | --- | --- |
| `so.Plot` | Declare data and variable mappings | `so.Plot(df, x='bill', y='tip', color='time')` |
| `.add(Mark, Stat, Move)` | Layer marks with optional statistics and position adjustments | `.add(so.Dot())`, `.add(so.Line(), so.PolyFit(order=2))` |
| Marks | Visual elements | `so.Dot`, `so.Line`, `so.Lines`, `so.Path`, `so.Bar`, `so.Bars`, `so.Band`, `so.Area`, `so.Text` |
| Stats | Transformations | `so.Agg('mean')`, `so.Est('mean', errorbar='sd')`, `so.Hist`, `so.KDE`, `so.PolyFit`, `so.Count` |
| Moves | Position adjustments | `so.Dodge()`, `so.Jitter()`, `so.Stack()`, `so.Shift()`, `so.Norm()` |
| Scales | Control mappings | `.scale(color='viridis', x='log')`, `so.Continuous`, `so.Nominal`, `so.Temporal` |
| Facets and pairs | Panels | `.facet(col='time')`, `.pair(x=['a', 'b'])` |
| Labels and limits | Annotation | `.label(title=, x=, y=)`, `.limit(x=(0, 10))` |
| Layout and theme | Size, style | `.layout(size=(8, 4))`, `.theme({...})` |
| Output | Show or save | `.show()`, `.save('p.png')`, `.on(ax)` to draw on a Matplotlib axes |

```python
import seaborn.objects as so
tips = sns.load_dataset('tips')

(so.Plot(tips, x='total_bill', y='tip', color='time')
   .add(so.Dot(alpha=.5))
   .add(so.Line(), so.PolyFit(order=1))
   .facet(col='day', wrap=2)
   .label(x='Total bill ($)', y='Tip ($)')
   .scale(color='Set2')
   .layout(size=(8, 5)))

# mean with error bars, dodged by group
(so.Plot(tips, x='day', y='total_bill', color='sex')
   .add(so.Bar(), so.Agg('mean'), so.Dodge())
   .add(so.Range(), so.Est(errorbar='se'), so.Dodge()))

# classic API for the same idea
sns.barplot(data=tips, x='day', y='total_bill', hue='sex', errorbar='se')
```

**Exercises**

1. Recreate a scatter-with-trend-line chart in both the classic and objects interfaces and compare readability.
2. Draw the same bar chart with 95% confidence intervals, standard deviations and prediction intervals; explain how the whiskers differ.
3. Build an objects-style histogram faceted by category with a KDE overlay.

## Module 11: Real-world EDA workflows and performance

Seaborn shines in exploratory data analysis: quick, consistent charts that guide your next question.

**A repeatable EDA workflow**

| Step | Question | Seaborn tools |
| --- | --- | --- |
| 1. Shape and types | What columns and types do I have? | pandas `info`, `describe` |
| 2. Missing data | Where are the gaps? | `sns.heatmap(df.isna(), cbar=False)` |
| 3. Single variables | How is each variable distributed? | `histplot`, `kdeplot`, `boxplot`, `countplot` |
| 4. Pairs | How do variables relate? | `pairplot`, `scatterplot`, `heatmap` of correlations |
| 5. Groups | Do segments differ? | `catplot`, `violinplot`, `barplot`, `displot(col=)` |
| 6. Time | Are there trends and seasonality? | `lineplot`, `relplot(kind='line')`, year-month heatmap |
| 7. Outliers | Which points are unusual? | `boxplot`, `scatterplot` with annotated points |
| 8. Communicate | What is the one message? | Simplify, label directly, apply a theme |

**Performance and scale tips**

| Problem | Remedy |
| --- | --- |
| Millions of points in a scatter | Use `histplot(x=, y=)`, `jointplot(kind='hex')`, or sample with `df.sample(n=50_000)` |
| Slow `swarmplot` | Use `stripplot` or sample; swarm is for small groups |
| Slow bootstrapped intervals | Lower `n_boot`, use `errorbar='sd'`, or `errorbar=None` |
| Slow `pairplot` | Use `corner=True`, fewer `vars=`, `kind='hist'`, or sample rows |
| Heavy figures in PDFs | `rasterized=True` for dense layers, or save as PNG |
| Category order surprises | Make columns `pd.Categorical` with ordered levels |
| Aggregate first | Pre-aggregate with `groupby` when you only need summaries |

```python
df = pd.read_csv('customers.csv', parse_dates=['signup'])

# missing-data map
sns.heatmap(df.isna(), cbar=False, yticklabels=False)

# numeric overview
num = df.select_dtypes('number')
sns.pairplot(num.sample(min(len(num), 2000), random_state=0),
             corner=True, kind='hist')

# group comparison on an ordered category
df['tier'] = pd.Categorical(df['tier'], ['basic', 'plus', 'pro'], ordered=True)
sns.boxplot(data=df, x='tier', y='spend', showfliers=False)

# seasonality
monthly = (df.assign(year=df['signup'].dt.year, month=df['signup'].dt.month)
             .pivot_table(index='month', columns='year', values='id', aggfunc='count'))
sns.heatmap(monthly, cmap='mako', annot=True, fmt='d')
```

**Exercises**

1. Apply the eight-step workflow to a dataset of your choice and write one sentence of insight for each step.
2. Replace a slow 1-million-point scatter with a hexbin or 2D histogram and compare render times.
3. Create an ordered categorical column so categories appear in logical order in every chart.

## Module 12: Chart selection guide and capstone projects

**Which Seaborn plot should I use?**

| Question | Plot | Function |
| --- | --- | --- |
| How is one numeric variable distributed? | Histogram, KDE, ECDF | `histplot`, `kdeplot`, `ecdfplot` |
| How do distributions compare across groups? | Box, violin, boxen, overlaid KDE | `boxplot`, `violinplot`, `boxenplot`, `kdeplot(hue=)` |
| How do categories compare on a summary value? | Bar or point with error bars | `barplot`, `pointplot` |
| How many observations per category? | Count bars | `countplot` |
| How do two numeric variables relate? | Scatter, hex or 2D histogram, regression | `scatterplot`, `jointplot`, `regplot` |
| How does a value change over time or order? | Line with uncertainty | `lineplot`, `relplot(kind='line')` |
| How do many variables relate at once? | Pair grid, correlation heatmap | `pairplot`, `heatmap` |
| Which rows or columns cluster? | Clustered heatmap | `clustermap` |
| How do results differ by segment? | Faceted charts | `col=`, `row=`, `FacetGrid` |
| Do the model residuals look random? | Residual plot | `residplot` |

**Where Seaborn is used**

| Domain | Typical work |
| --- | --- |
| Business analytics | Segment comparisons, funnel and retention heatmaps, revenue distributions |
| Data science and ML | EDA, feature distributions by class, confusion matrices, correlation screening |
| Science and research | Experimental group comparisons with confidence intervals, publication figures |
| Healthcare | Cohort distributions, lab values by group, survival-style ECDFs |
| A/B testing | Metric distributions and confidence intervals per variant |
| Teaching | Fast, readable statistical graphics for courses |

**Capstone projects**

| Project | Dataset idea | Skills tested | Deliverable |
| --- | --- | --- | --- |
| 1. Full EDA report | Titanic, Ames housing, or a Kaggle retail set | Distribution, categorical, correlation and group plots | Notebook with eight charts and written findings |
| 2. A/B test visual summary | Simulated or real conversion data | `barplot` with intervals, ECDFs, faceting | One-page figure explaining which variant wins and the uncertainty |
| 3. Penguins or iris classification explorer | Penguins dataset | `pairplot`, `jointplot`, `clustermap`, palettes | Visual guide to which features separate species |
| 4. Time-series seasonality study | Flights, energy or web traffic | `lineplot`, heatmaps, `relplot` | Dashboard figure showing trend and seasonality |
| 5. Publication figure set | Your own research data | Themes, contexts, custom palettes, Matplotlib tuning, vector export | Three consistent figures at journal size |
| 6. Objects-interface rebuild | Any earlier project | `seaborn.objects` layers, facets and scales | Same charts rebuilt in the objects interface with a comparison note |

```python
# A/B test summary figure
ab = pd.DataFrame({
    'variant': np.repeat(['A', 'B'], 500),
    'revenue': np.r_[np.random.default_rng(0).gamma(2, 10, 500),
                     np.random.default_rng(1).gamma(2.2, 10, 500)],
})
fig, axs = plt.subplots(1, 3, figsize=(13, 4), constrained_layout=True)
sns.barplot(data=ab, x='variant', y='revenue', errorbar=('ci', 95), ax=axs[0])
sns.ecdfplot(data=ab, x='revenue', hue='variant', ax=axs[1])
sns.boxplot(data=ab, x='variant', y='revenue', showfliers=False, ax=axs[2])
fig.suptitle('Revenue per user by variant')
```

**Practice sources:** the Seaborn example gallery (recreate five examples), built-in datasets, Kaggle datasets, and charts from articles you admire.

## Appendix: cheat sheet, pitfalls, checklist and resources

**Quick reference by task**

| I want to... | Use |
| --- | --- |
| Apply a look | `sns.set_theme(style=, context=, palette=)` |
| Relationships | `scatterplot`, `lineplot`, `relplot` |
| Distributions | `histplot`, `kdeplot`, `ecdfplot`, `displot`, `jointplot` |
| Category comparisons | `boxplot`, `violinplot`, `stripplot`, `swarmplot`, `barplot`, `pointplot`, `countplot`, `catplot` |
| Fitted lines | `regplot`, `lmplot`, `residplot` |
| Matrices | `heatmap`, `clustermap` |
| Grids of plots | `FacetGrid`, `PairGrid`, `pairplot`, `JointGrid`, or `col=` and `row=` |
| Pick colours | `color_palette`, `diverging_palette`, `cubehelix_palette`, `palette=` |
| Move the legend | `sns.move_legend(ax_or_grid, 'upper left', bbox_to_anchor=(1, 1))` |
| Remove borders | `sns.despine()` |
| Declarative plots | `import seaborn.objects as so` then `so.Plot(...).add(...)` |
| Save | `fig.savefig(...)` or `g.savefig(...)` |

**Common pitfalls**

1. Passing wide, messy data where tidy long data is expected. Reshape with `melt` first.
2. Treating a confidence interval of the mean as the spread of the data. Use `errorbar='sd'` or `('pi', 95)` to show spread.
3. Using `swarmplot` on thousands of points; it is slow and warns about overlap. Use `stripplot` or a violin.
4. Calling figure-level functions with `ax=`; they create their own figure. Use the axes-level function for custom subplots.
5. Starting bar charts above zero, which exaggerates differences.
6. Forgetting that Seaborn silently drops missing values; check counts first.
7. Alphabetical category order hiding the logical order. Set `order=` or use ordered categoricals.
8. Too many hue colours (more than about six) that cannot be told apart. Facet instead.
9. Using deprecated arguments after upgrading (for example `ci=` replaced by `errorbar=`, `sns.distplot` replaced by `histplot` or `displot`). Check release notes.
10. Rainbow palettes for ordered data. Use a sequential or diverging palette suited to the data.
11. Using regression plots as formal inference. They are exploratory; use `statsmodels` for real modelling.

**6-week practice plan**

| Weeks | Daily habit (45 to 60 minutes) |
| --- | --- |
| 1 to 2 | Read a module, type every example, finish the exercises |
| 3 to 4 | Recreate one gallery example per day with a different dataset |
| 5 | Rebuild three of your charts with the objects interface |
| 6 | Complete two capstones and publish them |

**Mastery checklist**

- [ ] I can explain figure-level vs axes-level functions and choose between them.
- [ ] I can prepare tidy data and map columns to `x`, `y`, `hue`, `size`, `style`, `col` and `row`.
- [ ] I can pick the right distribution, categorical or relational plot for a question.
- [ ] I can state exactly what an error bar or confidence band represents.
- [ ] I can build heatmaps and cluster maps from correlation or pivot tables.
- [ ] I can create faceted grids and pair plots for a quick multivariate overview.
- [ ] I can apply a theme and a colourblind-safe palette consistently.
- [ ] I can customise any Seaborn chart further with Matplotlib and export it for print or web.
- [ ] I can build a plot with the objects interface.

**Resources**

- Official Seaborn documentation: tutorial, API reference and example gallery (seaborn.pydata.org)
- The Seaborn paper by Michael Waskom in the Journal of Open Source Software
- *Fundamentals of Data Visualization* by Claus Wilke (free online)
- *Python Data Science Handbook* by Jake VanderPlas, visualisation chapters (free online)
- Next steps: the Matplotlib, pandas and NumPy courses in this series, then Plotly or Altair for interactive charts
