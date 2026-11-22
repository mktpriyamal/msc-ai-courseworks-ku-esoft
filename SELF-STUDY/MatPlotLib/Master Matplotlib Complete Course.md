# Master Matplotlib: Complete Course

Nov 21, 2026 · @Thisara Priyamal

This 8-week course takes you from your first `plt.plot()` to publication-quality figures, animations and custom components, covering the whole Matplotlib toolkit with a use case for each topic.

## Course overview

**Who it is for:** analysts, scientists, engineers and students who know basic Python and ideally some NumPy or pandas.

**What you will be able to do:** choose the right chart for your data, control every visual element, build multi-panel layouts, plot 2D, 3D and statistical data, animate results, and export figures that meet journal and web standards.

**How to use it:** each module has topics with descriptions, key API, use cases, a code sample and exercises. Type every example and finish the exercises before moving on.

| Week | Modules | Milestone |
| --- | --- | --- |
| 1 | 1 to 2 | Understand Figure and Axes; draw the core chart types |
| 2 | 3 to 4 | Style charts; control axes, ticks and scales |
| 3 | 5 to 6 | Build multi-panel layouts; statistical plots |
| 4 | 7 to 8 | Heatmaps, contours, 3D and polar plots |
| 5 | 9 to 10 | Themes, fonts, LaTeX; animation and interactivity |
| 6 | 11 to 12 | Export and integrate with pandas and seaborn |
| 7 | 13 | Artists, transforms and custom components |
| 8 | 14 | Finish two capstone projects |

```python
import matplotlib as mpl
import matplotlib.pyplot as plt
import numpy as np

print(mpl.__version__)
```

## Module 1: Setup, architecture and the two APIs

Understanding the object hierarchy removes most Matplotlib confusion.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Installation and backends | `pip install matplotlib`; backends render to screen or file (inline in Jupyter, interactive windows, Agg for files) | `matplotlib.use('Agg')`, `%matplotlib inline`, `%matplotlib widget` | Notebooks, servers, scripts |
| Anatomy of a figure | Figure contains one or more Axes; each Axes contains Axis, ticks, labels, lines, text and other Artists | `Figure`, `Axes`, `Axis`, `Artist` | Knowing what to modify |
| pyplot (stateful) API | Quick functions acting on the current figure and axes | `plt.plot`, `plt.title`, `plt.show` | Fast exploration |
| Object-oriented API | Explicit `fig` and `ax` objects; preferred for anything non-trivial | `fig, ax = plt.subplots()`, `ax.plot`, `ax.set_title` | Reusable, multi-panel code |
| Figure setup | Size, resolution, background | `figsize=(w, h)`, `dpi=`, `facecolor=` | Controlling output size |
| Showing and closing | Display and free memory | `plt.show()`, `plt.close(fig)`, `plt.close('all')` | Loops creating many figures |
| Plot lifecycle | Create, draw, adjust, save | `fig.canvas.draw()`, `fig.savefig` | Understanding when things render |
| Documentation habits | Gallery, cheatsheets, `help(ax.plot)` | matplotlib.org gallery | Finding examples quickly |

```python
x = np.linspace(0, 2 * np.pi, 200)

# object-oriented style (recommended)
fig, ax = plt.subplots(figsize=(6, 4), dpi=120)
ax.plot(x, np.sin(x), label='sin')
ax.plot(x, np.cos(x), label='cos')
ax.set_xlabel('x (radians)')
ax.set_ylabel('value')
ax.set_title('Sine and cosine')
ax.legend()
plt.show()
```

**Exercises**

1. Draw the same chart with pyplot functions and with `fig, ax`, then explain which is easier to reuse in a function.
2. Print `type()` of `fig`, `ax`, `ax.xaxis` and `ax.lines[0]` to confirm the hierarchy.
3. Create 20 figures in a loop and close each one; check memory use with and without `plt.close`.

## Module 2: Core plot types

Pick the chart that matches the question: trend, comparison, distribution, relationship or composition.

| Plot | Method | Best for | Key options |
| --- | --- | --- | --- |
| Line | `ax.plot(x, y)` | Trends over an ordered axis | `linestyle`, `linewidth`, `marker`, `color`, `label` |
| Scatter | `ax.scatter(x, y)` | Relationship between two variables | `s` (size), `c` (colour values), `alpha`, `cmap`, `marker` |
| Bar (vertical and horizontal) | `ax.bar`, `ax.barh` | Compare categories | `width`, `bottom` (stacking), `yerr`, `align`, `hatch` |
| Grouped and stacked bars | Offset x positions; `bottom=` | Compare sub-categories | `ax.bar_label` |
| Histogram | `ax.hist(data, bins=)` | Distribution of one variable | `density`, `cumulative`, `histtype`, `range` |
| Step | `ax.step`, `ax.stairs` | Piecewise-constant signals, histograms | `where='pre'/'mid'/'post'` |
| Fill between | `ax.fill_between`, `fill_betweenx` | Confidence bands, areas | `alpha`, `where=`, `interpolate` |
| Stackplot | `ax.stackplot` | Composition over time | `labels`, `colors` |
| Pie and donut | `ax.pie` | Few parts of a whole (use sparingly) | `autopct`, `explode`, `wedgeprops` |
| Error bars | `ax.errorbar` | Uncertainty | `yerr`, `xerr`, `capsize`, `fmt` |
| Reference lines | `ax.axhline`, `axvline`, `axhspan`, `axvspan` | Thresholds, highlighted periods | `linestyle`, `alpha` |
| Stem | `ax.stem` | Discrete signals | `linefmt`, `markerfmt` |
| Event plot | `ax.eventplot` | Spike trains, event timelines | `lineoffsets` |

```python
rng = np.random.default_rng(0)
fig, axs = plt.subplots(2, 2, figsize=(9, 6), constrained_layout=True)

x = np.arange(1, 13)
axs[0, 0].plot(x, rng.integers(50, 100, 12).cumsum(), marker='o')
axs[0, 0].set_title('Line')

axs[0, 1].scatter(rng.normal(size=100), rng.normal(size=100),
                  c=rng.random(100), s=40, cmap='viridis', alpha=.8)
axs[0, 1].set_title('Scatter')

cats = ['A', 'B', 'C']
bars = axs[1, 0].bar(cats, [23, 41, 30], color='#4C78A8')
axs[1, 0].bar_label(bars)
axs[1, 0].set_title('Bar')

axs[1, 1].hist(rng.normal(size=1000), bins=30, density=True, edgecolor='white')
axs[1, 1].set_title('Histogram')
plt.show()
```

**Exercises**

1. Draw a grouped bar chart of quarterly sales for three products with value labels.
2. Plot a noisy signal with a shaded one-standard-deviation band using `fill_between`.
3. Build a scatter plot where marker size encodes population and colour encodes continent.
4. Compare a 10-bin and 50-bin histogram of the same data and describe what changes.

## Module 3: Styling, text, annotations and legends

Clear labels and restrained styling matter more than decoration.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Colours | Names, hex, RGB tuples, grayscale strings, cycle colours `C0` to `C9` | `color='#1f77b4'`, `'tab:orange'` | Brand colours |
| Colour cycles | Default sequence of series colours | `ax.set_prop_cycle`, `rcParams['axes.prop_cycle']` | Consistent palettes |
| Lines | Style and width | `linestyle='--'`, `lw=`, `dashes=`, `drawstyle=` | Distinguish series |
| Markers | Shape, size, fill | `marker='o'`, `markersize`, `markerfacecolor`, `markeredgecolor`, `markevery` | Highlight data points |
| Format strings | Compact style | `ax.plot(x, y, 'ro--')` | Quick plots |
| Transparency and z-order | Layer elements | `alpha=`, `zorder=` | Overlapping data |
| Titles and labels | Describe the chart | `set_title`, `set_xlabel`, `set_ylabel`, `fig.suptitle`, `loc=`, `labelpad` | Every chart |
| Text | Place words | `ax.text(x, y, s, ha=, va=, fontsize=, transform=)` | Labels on data |
| Annotations | Text with arrows | `ax.annotate(text, xy, xytext, arrowprops=dict(arrowstyle='->'))` | Call out peaks, events |
| Legends | Series key | `ax.legend(loc=, ncol=, title=, frameon=, bbox_to_anchor=)`, `handles`, `labels` | Multiple series |
| Custom legend entries | Build proxies | `matplotlib.lines.Line2D`, `matplotlib.patches.Patch` | Legends for encodings |
| Colour accessibility | Colourblind-safe palettes, avoid red-green pairs | `plt.style.use('tableau-colorblind10')` | Inclusive design |
| Hatches and patterns | Distinguish in grayscale | `hatch='//'` | Black-and-white printing |
| Grids and reference marks | Aid reading values | `ax.grid(True, alpha=.3)` | Readability |

```python
x = np.linspace(0, 10, 100)
fig, ax = plt.subplots(figsize=(7, 4))

ax.plot(x, np.sin(x), color='tab:blue', lw=2, label='signal')
ax.plot(x, np.sin(x) * .5, 'r--', lw=1.5, label='half amplitude')

ax.annotate('peak', xy=(np.pi / 2, 1), xytext=(3, 1.15),
            arrowprops=dict(arrowstyle='->', color='gray'))
ax.axhline(0, color='black', lw=.8)
ax.axvspan(6, 8, color='gold', alpha=.2, label='event window')

ax.set(title='Annotated signal', xlabel='time (s)', ylabel='amplitude',
       ylim=(-1.3, 1.4))
ax.grid(alpha=.3)
ax.legend(loc='lower left', ncol=3, frameon=False)
```

**Exercises**

1. Recreate a published chart: match its colours, line styles, labels and annotations.
2. Build a legend that explains marker size (small, medium, large) using proxy artists.
3. Re-draw a red/green chart using a colourblind-safe palette and compare readability in grayscale.

## Module 4: Axes control: limits, ticks, scales, formatters and dates

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Limits | Zoom or fix ranges | `set_xlim`, `set_ylim`, `margins`, `autoscale`, `invert_yaxis` | Focus on a region |
| Aspect ratio | Equal scaling, fixed shapes | `ax.set_aspect('equal')`, `ax.set_box_aspect` | Maps, geometry |
| Ticks | Positions and labels | `set_xticks`, `set_xticklabels`, `tick_params(direction=, length=, labelrotation=)` | Readable axes |
| Locators | Automatic tick placement | `MultipleLocator`, `MaxNLocator`, `LogLocator`, `AutoMinorLocator`, `FixedLocator` | Neat tick spacing |
| Formatters | Tick label text | `FuncFormatter`, `StrMethodFormatter('{x:,.0f}')`, `PercentFormatter`, `ScalarFormatter` | Currency, percentages |
| Scales | Non-linear axes | `set_xscale('log'/'symlog'/'logit')`, `set_yscale`, custom `FuncScale` | Wide-ranging data |
| Dates | Time axes | `matplotlib.dates`: `DateFormatter`, `AutoDateLocator`, `ConciseDateFormatter`, `MonthLocator` | Time series |
| Spines | Axes borders | `ax.spines['top'].set_visible(False)`, `set_position` | Minimal style, crossed axes |
| Twin axes | Two y-scales | `ax.twinx()`, `ax.twiny()` | Different units on one chart |
| Secondary axes | Derived scale | `ax.secondary_xaxis(location, functions=(f, inv))` | Celsius and Fahrenheit |
| Grids | Major and minor | `ax.grid(which='both')`, `minorticks_on` | Reading values |
| Axis labels and offsets | Large-number notation | `ax.ticklabel_format`, `ax.yaxis.get_offset_text` | Scientific data |
| Broken and inverted axes | Special layouts | Two stacked axes; `invert_xaxis` | Outlier-heavy data |

```python
import matplotlib.dates as mdates
import matplotlib.ticker as mticker
import pandas as pd

dates = pd.date_range('2026-01-01', periods=365)
vals = np.random.default_rng(1).normal(0, 1, 365).cumsum() * 1000 + 50000

fig, ax = plt.subplots(figsize=(8, 3.5))
ax.plot(dates, vals)
ax.xaxis.set_major_locator(mdates.MonthLocator())
ax.xaxis.set_major_formatter(mdates.DateFormatter('%b'))
ax.yaxis.set_major_formatter(mticker.StrMethodFormatter('${x:,.0f}'))
for s in ('top', 'right'):
    ax.spines[s].set_visible(False)

ax2 = ax.twinx()
ax2.set_ylabel('secondary axis')
ax.set_yscale('linear')
```

**Exercises**

1. Plot values spanning six orders of magnitude on a log axis with sensible minor ticks.
2. Format a revenue axis as `$1.2M` using `FuncFormatter`.
3. Show temperature in Celsius with a secondary Fahrenheit axis.
4. Plot one year of daily data with month labels centred between ticks.

## Module 5: Subplots and layouts

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Grid of subplots | Rows and columns of axes | `plt.subplots(nrows, ncols, sharex=, sharey=, squeeze=)` | Small multiples |
| Layout engines | Prevent overlaps automatically | `constrained_layout=True`, `layout='constrained'`, `tight_layout()` | Clean spacing |
| Manual spacing | Fine control | `fig.subplots_adjust(left, right, hspace, wspace)` | Custom margins |
| Mosaic layouts | Name the layout in text | `fig.subplot_mosaic([['A','A','B'],['C','D','B']])` | Dashboards |
| GridSpec | Cells of different sizes | `fig.add_gridspec(2, 3, width_ratios=, height_ratios=)`, `GridSpecFromSubplotSpec` | Main plot with side panels |
| Single axes anywhere | Position by fraction | `fig.add_axes([l, b, w, h])`, `fig.add_subplot` | Insets, custom figures |
| Inset axes | Zoom panels | `ax.inset_axes`, `indicate_inset_zoom` | Zoom into detail |
| Shared axes | Align scales | `sharex='col'`, `sharey='row'` | Comparable panels |
| Sub-figures | Nested figure groups | `fig.subfigures(nrows, ncols)` | Complex figure sets |
| Figure-level text | Titles and labels spanning panels | `fig.suptitle`, `fig.supxlabel`, `fig.supylabel` | Shared labels |
| Colourbar sharing | One bar for many panels | `fig.colorbar(im, ax=axs)` | Heatmap grids |
| Axes grid helpers | Specialised layouts | `mpl_toolkits.axes_grid1.make_axes_locatable`, `ImageGrid` | Image galleries |
| Looping over axes | Efficient panel creation | `for ax, col in zip(axs.flat, cols):` | Plot every column |

```python
rng = np.random.default_rng(2)
x, y = rng.normal(size=500), rng.normal(size=500)

fig, axd = plt.subplot_mosaic(
    [['top', 'top', '.'],
     ['main', 'main', 'right']],
    width_ratios=[3, 3, 1], height_ratios=[1, 3],
    constrained_layout=True, figsize=(7, 6), sharex=False)

axd['main'].scatter(x, y, s=10, alpha=.6)
axd['top'].hist(x, bins=30, color='gray')
axd['right'].hist(y, bins=30, orientation='horizontal', color='gray')
axd['top'].set_xticklabels([])
axd['right'].set_yticklabels([])
fig.suptitle('Scatter with marginal histograms')
```

**Exercises**

1. Draw a 3x3 grid of small multiples for nine categories with shared axes and one common legend.
2. Build a dashboard layout with `subplot_mosaic`: one wide time series on top and three small charts below.
3. Add a zoomed inset to a time series showing a short spike.

## Module 6: Statistical and distribution plots

| Plot | Method | Shows | Use case |
| --- | --- | --- | --- |
| Box plot | `ax.boxplot(data, labels=, showmeans=, notch=)` | Median, quartiles, outliers | Compare groups |
| Violin plot | `ax.violinplot(data, showmedians=True)` | Full distribution shape | Bimodal data |
| Histogram variants | `hist(histtype='step', density=True, cumulative=True)`, `ax.ecdf` (newer versions) | Distribution, cumulative share | Overlay groups |
| 2D histogram | `ax.hist2d`, `ax.hexbin` | Density of point clouds | Large scatter data |
| Kernel density idea | Compute with SciPy or pandas then `ax.plot` / `fill_between` | Smooth distribution | Presentable densities |
| Error bars | `ax.errorbar(x, y, yerr=, fmt='o', capsize=3)` | Mean with uncertainty | Experiment results |
| Q-Q and probability plots | Plot sorted data vs theoretical quantiles | Normality | Model checks |
| Scatter with regression | `np.polyfit` plus `ax.plot` | Trend line | Simple fits |
| Correlation matrix | `ax.imshow(corr)` with annotations | Pairwise relationships | Feature screening |
| Pair grids | Loop over column pairs with subplots | Many relationships | Exploratory analysis |
| Cumulative and survival curves | `ax.step`, `ax.ecdf` | Time-to-event | Reliability |
| Residual plots | Scatter of residuals vs fitted | Model diagnostics | Regression checks |
| Statistical shortcuts | seaborn for violin, KDE, pair and regression plots built on Matplotlib | `sns.boxplot`, `sns.pairplot` | Faster statistics visuals |

```python
rng = np.random.default_rng(4)
groups = [rng.normal(m, s, 200) for m, s in [(50, 5), (55, 8), (48, 4)]]

fig, axs = plt.subplots(1, 3, figsize=(11, 3.5), constrained_layout=True)

axs[0].boxplot(groups, tick_labels=['A', 'B', 'C'], showmeans=True)
axs[0].set_title('Box')

axs[1].violinplot(groups, showmedians=True)
axs[1].set_xticks([1, 2, 3], ['A', 'B', 'C'])
axs[1].set_title('Violin')

x, y = rng.normal(size=5000), rng.normal(size=5000)
hb = axs[2].hexbin(x, y, gridsize=30, cmap='Blues')
fig.colorbar(hb, ax=axs[2], label='count')
axs[2].set_title('Hexbin')
```

Note: the box plot label argument was renamed from `labels` to `tick_labels` in recent Matplotlib releases; check your version.

**Exercises**

1. Compare three groups using box and violin plots side by side and explain what each reveals.
2. Draw a hexbin of 1 million points and add a colourbar labelled with counts.
3. Plot means with 95% confidence intervals using `errorbar`.
4. Build a Q-Q plot for a sample and judge normality.

## Module 7: 2D data: images, heatmaps, contours, colormaps and vector fields

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Images | Display arrays as pixels | `ax.imshow(a, cmap=, vmin=, vmax=, origin=, extent=, interpolation=)` | Photos, matrices |
| Heatmaps | Annotated matrices | `imshow` plus `ax.text` per cell; `pcolormesh` | Correlation, confusion matrices |
| pcolormesh | Irregular or non-uniform grids | `ax.pcolormesh(X, Y, Z, shading='auto')` | Gridded data |
| Contours | Level lines and fills | `ax.contour`, `ax.contourf`, `clabel`, `levels=` | Elevation, surfaces |
| Colormaps | Map values to colours | `viridis`, `plasma`, `magma`, `coolwarm`, `RdBu`, `Greys`, `matplotlib.colormaps['name']` | Encoding magnitude |
| Choosing colormaps | Sequential for ordered data, diverging for data around a centre, qualitative for categories | Perceptually uniform maps preferred | Honest visuals |
| Normalisation | Control colour scaling | `Normalize`, `LogNorm`, `TwoSlopeNorm`, `BoundaryNorm`, `PowerNorm` | Skewed data, centred zero |
| Colourbars | Legend for colour | `fig.colorbar(im, ax=ax, label=, shrink=, orientation=)` | Every colour-coded plot |
| Custom colormaps | Build your own | `ListedColormap`, `LinearSegmentedColormap.from_list` | Branded scales |
| Vector fields | Arrows and flow | `ax.quiver`, `ax.streamplot`, `ax.barbs` | Wind, fluid flow |
| Triangulated data | Scattered points | `ax.tricontourf`, `ax.tripcolor`, `matplotlib.tri.Triangulation` | Sensor networks |
| Image I/O | Read and save images | `plt.imread`, `plt.imsave` | Image processing |
| Spectrograms and matrices | Specialised views | `ax.specgram`, `ax.matshow`, `ax.spy` | Audio, sparse matrices |

```python
from matplotlib.colors import TwoSlopeNorm

x = np.linspace(-3, 3, 200)
X, Y = np.meshgrid(x, x)
Z = np.exp(-(X**2 + Y**2)) - 0.5 * np.exp(-((X - 1)**2 + (Y - 1)**2))

fig, axs = plt.subplots(1, 3, figsize=(12, 3.8), constrained_layout=True)

im = axs[0].imshow(Z, extent=(-3, 3, -3, 3), origin='lower', cmap='viridis')
fig.colorbar(im, ax=axs[0], label='value')

cs = axs[1].contourf(X, Y, Z, levels=15, cmap='RdBu_r',
                     norm=TwoSlopeNorm(0, Z.min(), Z.max()))
axs[1].contour(X, Y, Z, levels=15, colors='k', linewidths=.3)
fig.colorbar(cs, ax=axs[1])

axs[2].streamplot(X, Y, -Y, X, color=np.hypot(X, Y), cmap='plasma', density=1.2)
axs[2].set_aspect('equal')
```

**Exercises**

1. Draw an annotated correlation heatmap with a diverging colormap centred at zero.
2. Plot a function of two variables as filled contours with labelled contour lines.
3. Display an image, then show its three colour channels as separate panels.
4. Create a custom colormap that goes from white to your brand colour.

## Module 8: 3D, polar and specialised plots

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| 3D axes | Create a 3D projection | `fig.add_subplot(projection='3d')` | Surfaces, trajectories |
| 3D lines and scatter | Points and paths in space | `ax.plot3D`, `ax.scatter3D` | Particle paths |
| Surfaces | Functions of two variables | `ax.plot_surface`, `plot_wireframe`, `plot_trisurf` | Cost landscapes |
| 3D contours and bars | Projections and columns | `ax.contour3D`, `ax.bar3d`, `contourf(zdir=)` | Comparisons in 3D |
| Camera and limits | View angle and ranges | `ax.view_init(elev, azim)`, `set_zlim`, `set_box_aspect` | Readable 3D |
| Voxels and quivers | Volumes and arrows | `ax.voxels`, `ax.quiver` | Volume data, fields |
| Polar plots | Angle and radius | `projection='polar'`, `ax.set_theta_zero_location`, `set_rticks` | Directions, cycles |
| Radar charts | Multi-metric comparison on polar axes | Polar `plot` plus `fill` | Skill or product profiles |
| Geographic projections | Built-in map-like axes | `projection='mollweide'/'hammer'/'aitoff'/'lambert'` | World views |
| Maps | Full cartography with companion libraries | Cartopy, GeoPandas `plot` | Geospatial analysis |
| Network and tree diagrams | Drawn with patches and lines, or companion tools | `matplotlib.patches`, NetworkX `draw` | Graphs |
| Table in a figure | Embed data | `ax.table(cellText=, colLabels=)` | Summary beside chart |
| Sankey and other specials | Flow diagrams | `matplotlib.sankey.Sankey` | Resource flows |
| Waterfall and Gantt idea | Build from bars | `ax.bar(bottom=)`, `ax.barh(left=)` | Finance, schedules |

```python
x = np.linspace(-3, 3, 60)
X, Y = np.meshgrid(x, x)
Z = np.sin(np.hypot(X, Y))

fig = plt.figure(figsize=(10, 4.5))
ax1 = fig.add_subplot(1, 2, 1, projection='3d')
ax1.plot_surface(X, Y, Z, cmap='viridis', edgecolor='none')
ax1.view_init(elev=30, azim=-60)

ax2 = fig.add_subplot(1, 2, 2, projection='polar')
labels = ['Speed', 'Power', 'Range', 'Cost', 'Comfort']
vals = [4, 3, 5, 2, 4]
ang = np.linspace(0, 2 * np.pi, len(vals), endpoint=False)
ang = np.r_[ang, ang[0]]; vals = vals + vals[:1]
ax2.plot(ang, vals); ax2.fill(ang, vals, alpha=.25)
ax2.set_xticks(ang[:-1], labels)
```

**Exercises**

1. Plot a 3D surface of a two-variable function and animate the camera angle in a loop.
2. Draw a radar chart comparing three products across six metrics.
3. Plot a wind-rose style bar chart on polar axes.
4. Draw a 3D scatter of a clustered dataset coloured by cluster.

## Module 9: Styles, rcParams, fonts and LaTeX

Consistent styling across a project comes from configuration, not repeated per-chart tweaks.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Built-in styles | Preset looks | `plt.style.available`, `plt.style.use('ggplot'/'seaborn-v0_8'/'bmh'/'dark_background'/'fivethirtyeight')` | Quick theming |
| Temporary styles | Scoped change | `with plt.style.context('ggplot'):` | One figure only |
| rcParams | Global defaults | `plt.rcParams['font.size'] = 12`, `mpl.rc('lines', linewidth=2)`, `plt.rcdefaults()` | Project defaults |
| Style sheets | Reusable `.mplstyle` files | `plt.style.use('mystyle.mplstyle')` | Team branding |
| Fonts | Family, size, weight | `font.family`, `font.sans-serif`, `font_manager.FontProperties`, `fontdict=` | Brand fonts |
| Math text | LaTeX-like math without LaTeX installed | `r'$\alpha^2 + \beta_i$'`, `mathtext.fontset` | Equations in labels |
| Full LaTeX | Typeset text with LaTeX | `plt.rcParams['text.usetex'] = True` | Journals using LaTeX |
| Dark and light themes | Backgrounds and contrast | `figure.facecolor`, `axes.facecolor`, `text.color` | Slides, dashboards |
| Colour cycles | Palette defaults | `axes.prop_cycle` with `cycler` | Consistent series colours |
| Unicode and languages | Non-Latin text | Choose a font that has the glyphs | Multilingual charts |
| Sizing for context | Scale for paper, slides, posters | `figure.figsize`, `font.size`, `savefig.dpi` | Fit the medium |
| Design principles | Remove clutter, label directly, limit colours, start bars at zero | Tufte-style data-ink focus | Honest, clear charts |

```python
from cycler import cycler

plt.rcParams.update({
    'figure.figsize': (7, 4),
    'figure.dpi': 120,
    'font.size': 11,
    'axes.spines.top': False,
    'axes.spines.right': False,
    'axes.grid': True,
    'grid.alpha': .3,
    'axes.prop_cycle': cycler(color=['#4C78A8', '#F58518', '#54A24B', '#E45756']),
    'savefig.bbox': 'tight',
})

fig, ax = plt.subplots()
ax.plot([1, 2, 3], [2, 4, 3], label=r'$y = \alpha x^2$')
ax.legend()
```

Save the same settings in a file named `team.mplstyle` (one `key: value` per line) and load it with `plt.style.use`.

**Exercises**

1. Create a `.mplstyle` file that matches your organisation's colours and fonts, then apply it to three different charts.
2. Render an equation in an axis label using mathtext.
3. Produce light and dark versions of the same chart using style contexts.

## Module 10: Animation and interactivity

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| FuncAnimation | Redraw each frame with an update function | `matplotlib.animation.FuncAnimation(fig, update, frames=, interval=, blit=True)` | Simulations, growing time series |
| ArtistAnimation | Pre-built list of frames | `ArtistAnimation(fig, artists)` | Image sequences |
| Saving animations | GIF, MP4 | `anim.save('a.gif', writer='pillow', fps=20)`, `writer='ffmpeg'` | Sharing |
| Notebook display | Show in Jupyter | `HTML(anim.to_jshtml())`, `%matplotlib widget` | Teaching, demos |
| Event handling | React to mouse and keyboard | `fig.canvas.mpl_connect('button_press_event', handler)`, `key_press_event`, `pick_event` | Click to select points |
| Picking | Select artists | `artist.set_picker(True)` | Interactive data exploration |
| Widgets | Sliders, buttons, checkboxes, text boxes | `matplotlib.widgets.Slider`, `Button`, `CheckButtons`, `RadioButtons`, `RectangleSelector` | Parameter tuning |
| Interactive backends | Zoom, pan, save toolbar | `%matplotlib qt`, `tk`, `widget`, `ipympl` | Exploration |
| Cursor tools | Crosshair and readouts | `matplotlib.widgets.Cursor`, `MultiCursor`, `mplcursors` | Reading values |
| Live updating plots | Update data without redrawing everything | `line.set_data`, `ax.relim()`, `fig.canvas.draw_idle()`, `flush_events` | Streaming data |
| Limits of Matplotlib interactivity | For web dashboards, pair with Plotly, Bokeh or Streamlit | Companion tools | Shareable apps |

```python
from matplotlib.animation import FuncAnimation

fig, ax = plt.subplots()
x = np.linspace(0, 2 * np.pi, 200)
line, = ax.plot(x, np.sin(x))
ax.set_ylim(-1.2, 1.2)

def update(frame):
    line.set_ydata(np.sin(x + frame / 10))
    return (line,)

anim = FuncAnimation(fig, update, frames=100, interval=40, blit=True)
anim.save('wave.gif', writer='pillow', fps=25)

# interactive slider
from matplotlib.widgets import Slider
fig2, ax2 = plt.subplots()
plt.subplots_adjust(bottom=.2)
l, = ax2.plot(x, np.sin(x))
sl = Slider(plt.axes([.2, .05, .6, .03]), 'freq', 1, 5, valinit=1)
sl.on_changed(lambda v: (l.set_ydata(np.sin(v * x)), fig2.canvas.draw_idle()))
```

**Exercises**

1. Animate a bouncing ball or orbiting planet and save it as a GIF.
2. Build a slider that changes the frequency and amplitude of a wave interactively.
3. Create a click handler that prints the coordinates of the nearest data point.

## Module 11: Saving, export and publication quality

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Saving figures | Write to file | `fig.savefig('f.png', dpi=300, bbox_inches='tight', transparent=False, facecolor='white')` | Reports, slides |
| File formats | PNG, JPG for raster; PDF, SVG, EPS for vector | `format=` or file extension | Choose by destination |
| Raster vs vector | Raster for dense images; vector for line art that must scale or be edited | PDF/SVG for papers | Journals and posters |
| Resolution | Pixels = inches x dpi | `figsize`, `dpi` | Sharp prints |
| Journal sizing | Match column width (often about 3.3 inches single column) and font sizes | `figsize=(3.4, 2.4)` | Submissions |
| Fonts in PDF/PS | Embed editable text | `pdf.fonttype = 42`, `ps.fonttype = 42` | Journal editing workflows |
| Multi-page PDFs | Many figures in one file | `matplotlib.backends.backend_pdf.PdfPages` | Report appendices |
| Transparent backgrounds | For overlaying on slides | `transparent=True` | Presentations |
| Metadata | Embed title, author | `metadata={'Title': ...}` | Provenance |
| Batch figure generation | One chart per group | Loop, `savefig`, `plt.close(fig)` | Automated reporting |
| Reproducible figures | Script every figure; seed random data | Version-controlled scripts | Auditable research |
| Accessibility | Alt text, contrast, colourblind palettes, direct labels | Documentation outside the image | Inclusive publishing |
| Embedding in apps | Use figures in web or GUI apps | `FigureCanvasAgg`, `io.BytesIO`, Streamlit `st.pyplot` | Dashboards and APIs |

```python
from matplotlib.backends.backend_pdf import PdfPages

plt.rcParams['pdf.fonttype'] = 42

fig, ax = plt.subplots(figsize=(3.4, 2.4))
ax.plot([1, 2, 3], [3, 1, 2])
ax.set(xlabel='time (s)', ylabel='signal')
fig.savefig('figure1.pdf', bbox_inches='tight')
fig.savefig('figure1.png', dpi=300, bbox_inches='tight')

with PdfPages('report.pdf') as pdf:
    for region in ['North', 'South', 'East']:
        f, a = plt.subplots()
        a.set_title(region)
        pdf.savefig(f)
        plt.close(f)
```

**Exercises**

1. Export one chart as PNG at 300 dpi and as SVG; compare file sizes and zoom quality.
2. Resize a chart to a journal single-column width and adjust fonts so text stays legible.
3. Generate a multi-page PDF with one chart per product category.

## Module 12: Integration (pandas, NumPy, seaborn) and performance

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| pandas plotting | DataFrame and Series methods draw with Matplotlib and return an `Axes` | `df.plot(ax=ax, kind=, x=, y=)`, `df.hist`, `df.boxplot` | Fast exploration, then refine with `ax` |
| Refining pandas plots | Use the returned axes | `ax = df.plot(); ax.set_title(...)` | Polished charts |
| NumPy data | Arrays feed every plot function directly | `ax.plot(np_array)`, `imshow`, `contourf` | Scientific data |
| Seaborn | High-level statistical charts built on Matplotlib; accepts `ax=` | `sns.scatterplot`, `sns.histplot`, `sns.heatmap`, `sns.pairplot`, `sns.lmplot` | Faster statistical visuals |
| Mixing libraries | Draw seaborn on your own axes and tweak with Matplotlib | `sns.boxplot(data=df, ax=ax)` | Custom dashboards |
| Named data | Plot columns by name | `ax.plot('x', 'y', data=df)` | Cleaner code |
| Categorical and string axes | Plot categories directly | `ax.bar(['A','B'], [1, 2])` | Labels without manual ticks |
| Large data | Avoid drawing millions of artists | `rasterized=True`, `ax.hexbin`, `hist2d`, downsample, `plt.plot(..., '.', markersize=1)`, `agg.path.chunksize` | Speed |
| Fast redraws | Reuse artists | `set_data`, `blit`, `canvas.draw_idle` | Live displays |
| Memory | Many open figures use RAM | `plt.close`, `plt.rcParams['figure.max_open_warning']` | Batch jobs |
| Server use | Headless rendering | `matplotlib.use('Agg')`, object-oriented API, `Figure()` without pyplot | Web apps, schedulers |
| Threading | pyplot is not thread-safe | `matplotlib.figure.Figure` per thread | Concurrent rendering |
| Alternatives to know | Plotly (interactive web), Altair (declarative), Bokeh (web apps), Datashader (huge data), Plotnine (grammar of graphics) | Companion libraries | Choosing the right tool |

```python
import pandas as pd
import seaborn as sns

df = pd.read_csv('sales.csv', parse_dates=['date'])

fig, axs = plt.subplots(1, 2, figsize=(11, 4), constrained_layout=True)

(df.set_index('date')['revenue'].resample('ME').sum()
   .plot(ax=axs[0], marker='o', title='Monthly revenue'))

sns.boxplot(data=df, x='region', y='revenue', ax=axs[1])
axs[1].set_title('Revenue by region')
axs[1].tick_params(axis='x', rotation=30)

# huge scatter: aggregate instead of plotting every point
big = np.random.default_rng(0).normal(size=(2_000_000, 2))
fig2, ax2 = plt.subplots()
ax2.hexbin(big[:, 0], big[:, 1], gridsize=80, bins='log')
```

**Exercises**

1. Plot a pandas DataFrame, then style the returned axes with annotations and a custom legend.
2. Time scatter plots of 1,000 vs 1 million points and replace the large one with hexbin.
3. Render a chart on a headless server using the Agg backend and the object-oriented API only.

## Module 13: Advanced internals: artists, transforms, patches and custom components

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Artist hierarchy | Everything drawn is an Artist; containers hold child artists | `ax.get_children()`, `ax.lines`, `ax.patches`, `ax.texts` | Inspect and modify charts after creation |
| Patches | Shapes: rectangles, circles, polygons, arrows, wedges | `matplotlib.patches.Rectangle`, `Circle`, `Polygon`, `FancyArrowPatch`, `FancyBboxPatch` | Diagrams, highlight regions |
| Collections | Many artists drawn efficiently | `LineCollection`, `PatchCollection`, `PolyCollection`, `scatter` returns `PathCollection` | Large drawings |
| Paths | Arbitrary vector shapes | `matplotlib.path.Path`, `PathPatch` | Custom markers and shapes |
| Transforms | Move between coordinate systems | `ax.transData`, `ax.transAxes`, `fig.transFigure`, `blended_transform_factory`, `ScaledTranslation` | Place text at axes fractions |
| Custom markers | Build your own symbols | `marker=Path(...)`, `MarkerStyle` | Special icons |
| Path effects | Outline, shadow, glow | `matplotlib.patheffects.withStroke`, `SimpleLineShadow` | Readable labels on busy backgrounds |
| Image overlay | Place images on charts | `OffsetImage`, `AnnotationBbox`, `ax.imshow(extent=)` | Logos, icons as markers |
| Draw order | Control layers | `zorder`, `set_rasterized`, `set_animated` | Overlap correctness |
| Custom projections and scales | Extend axes | `matplotlib.projections.register_projection`, `ScaleBase` | Specialised charts |
| Custom plot functions | Wrap repeated plots in functions taking `ax` | `def plot_x(ax, data, **kw):` | Reusable code |
| Units and converters | Plot custom types | `matplotlib.units.registry` | Quantities with units |
| Backends | Rendering engines | Agg, Cairo, PDF, SVG, Qt, Tk, WebAgg | Output and interactivity choices |
| Testing figures | Compare images in tests | `matplotlib.testing.decorators.image_comparison`, `pytest-mpl` | Visual regression tests |

```python
import matplotlib.patheffects as pe
from matplotlib.patches import Rectangle, FancyArrowPatch

fig, ax = plt.subplots(figsize=(6, 4))
ax.set(xlim=(0, 10), ylim=(0, 6), aspect='equal')

ax.add_patch(Rectangle((1, 1), 3, 2, fc='#D6E4F0', ec='#4C78A8', lw=2))
ax.add_patch(Rectangle((6, 3), 3, 2, fc='#FCE5CD', ec='#F58518', lw=2))
ax.add_patch(FancyArrowPatch((4, 2), (6, 4), arrowstyle='-|>', mutation_scale=18))

# text placed in axes-fraction coordinates, outlined for legibility
ax.text(.5, .95, 'Data flow', transform=ax.transAxes, ha='center', fontsize=14,
        path_effects=[pe.withStroke(linewidth=3, foreground='white')])

# reusable plotting function
def plot_series(ax, x, y, **kw):
    ax.plot(x, y, **kw)
    ax.grid(alpha=.3)
    return ax
```

**Exercises**

1. Draw a simple architecture diagram using only patches and annotations.
2. Add a text label at the top-right of every subplot using `transAxes` so it stays in place when limits change.
3. Write a reusable `plot_with_band(ax, x, mean, std)` function and use it in four panels.

## Module 14: Use cases and capstone projects

**Choosing the right chart**

| Question | Chart | Avoid |
| --- | --- | --- |
| How does a value change over time? | Line, area, step | Pie charts, 3D bars |
| How do categories compare? | Sorted horizontal or vertical bars starting at zero | Truncated bar axes |
| What is the distribution? | Histogram, box, violin, ECDF | Averages only |
| How do two variables relate? | Scatter with trend line; hexbin for large data | Dual axes that imply false correlation |
| What is the composition? | Stacked bars or a few-slice pie or donut | Pies with many slices |
| Where do values sit on a grid or map? | Heatmap, contour, image | Rainbow colormaps |
| How uncertain is the estimate? | Error bars, confidence bands | Hiding uncertainty |

**Where Matplotlib is used**

| Domain | Typical work | Modules used |
| --- | --- | --- |
| Business reporting | KPI trends, regional comparisons, automated PDF reports | 2, 3, 5, 11 |
| Data science | EDA panels, model diagnostics, confusion matrices, learning curves | 5, 6, 7 |
| Science and engineering | Publication figures, signals, spectra, simulations | 4, 7, 9, 11 |
| Finance | Candlestick-style charts built from bars, drawdown and return charts | 2, 4, 12 |
| Geospatial | Maps with GeoPandas or Cartopy | 8, 12 |
| Education | Animated explanations and interactive demos | 10 |

**Capstone projects**

| Project | Skills tested | Deliverable |
| --- | --- | --- |
| 1. Sales dashboard figure | Mosaic layout, formatters, annotations, pandas integration | One multi-panel PNG and PDF with six charts |
| 2. Publication-ready figure set | Style file, journal sizing, vector export, LaTeX text | Three consistent figures meeting a journal's size and font rules |
| 3. Model diagnostics report | Residuals, ROC curve, confusion matrix heatmap, learning curves | Multi-page PDF using `PdfPages` |
| 4. Animated simulation | `FuncAnimation`, physics or random walk | GIF or MP4 of a moving system |
| 5. Custom chart type | Patches, transforms, reusable functions | A waterfall, Gantt or bullet chart function usable on any axes |
| 6. Automated report generator | Loops, headless Agg backend, batch export | Script producing one chart per region every run |

```python
# Waterfall chart (custom chart type from bars)
def waterfall(ax, labels, deltas):
    cum = np.cumsum([0] + list(deltas))
    for i, d in enumerate(deltas):
        ax.bar(i, d, bottom=cum[i], color='#54A24B' if d >= 0 else '#E45756')
    ax.bar(len(deltas), cum[-1], color='#4C78A8')
    ax.set_xticks(range(len(deltas) + 1), list(labels) + ['Total'])
    return ax

fig, ax = plt.subplots(figsize=(7, 3.5))
waterfall(ax, ['Start', 'Sales', 'Costs', 'Tax'], [100, 60, -45, -15])
ax.set_title('Profit bridge')
```

**Practice sources:** the official Matplotlib gallery (recreate five examples), public datasets from Our World in Data and Kaggle, and charts from news sites you admire (reproduce them).

## Appendix: cheat sheet, pitfalls, checklist and resources

**Quick reference by task**

| I want to... | Use |
| --- | --- |
| Start a figure | `fig, ax = plt.subplots(figsize=, dpi=)` |
| Draw data | `plot`, `scatter`, `bar`, `hist`, `imshow`, `contourf`, `boxplot` |
| Label | `ax.set(title=, xlabel=, ylabel=)`, `annotate`, `text`, `legend` |
| Control axes | `set_xlim`, `set_xscale`, `set_xticks`, `tick_params`, formatters and locators |
| Several panels | `subplots`, `subplot_mosaic`, `GridSpec`, `constrained_layout` |
| Colour data | `cmap=`, `norm=`, `fig.colorbar` |
| Style globally | `plt.style.use`, `rcParams`, `.mplstyle` files |
| Animate | `FuncAnimation` and `anim.save` |
| Export | `fig.savefig('f.pdf', bbox_inches='tight')` |
| Plot from pandas | `df.plot(ax=ax)` then refine with the axes object |

**Common pitfalls**

1. Mixing pyplot calls and `ax` methods in the same figure, so changes land on the wrong axes. Use the object-oriented API.
2. Forgetting `plt.close(fig)` in loops, which leaks memory.
3. Overlapping labels. Use `constrained_layout=True` and rotate or shorten tick labels.
4. Using rainbow or `jet` colormaps that distort perception. Prefer `viridis` or a diverging map for centred data.
5. Truncating bar chart axes, which exaggerates differences. Bars should start at zero.
6. Plotting millions of points directly. Aggregate with `hexbin` or `hist2d`.
7. Saving before layout is final, giving cut-off labels. Use `bbox_inches='tight'`.
8. Relying on defaults for fonts and sizes that look tiny in slides or huge in papers. Size the figure for its destination.
9. Encoding information by colour alone. Add markers, labels or hatching for accessibility.
10. Using deprecated or renamed arguments after upgrading (for example `labels` vs `tick_labels` in box plots, or old seaborn style names). Check release notes.
11. Two y-axes that suggest a relationship that does not exist. Prefer separate panels.

**8-week practice plan**

| Weeks | Daily habit (45 to 60 minutes) |
| --- | --- |
| 1 to 3 | Read a module, type every example, finish the exercises |
| 4 to 5 | Recreate one gallery example or news chart per day |
| 6 to 7 | Convert your own project data into a styled figure set with a shared `.mplstyle` |
| 8 | Complete two capstones and publish them |

**Mastery checklist**

- [ ] I can explain Figure, Axes, Axis and Artist and navigate between them.
- [ ] I can pick the right chart type for a given question.
- [ ] I can format axes, ticks, dates and units exactly as I want.
- [ ] I can build multi-panel layouts with shared axes and a single legend or colourbar.
- [ ] I can choose colormaps and normalisations that represent data honestly.
- [ ] I can apply a reusable style across a whole project.
- [ ] I can export figures at the right size, resolution and format for print and web.
- [ ] I can animate a simulation and add simple interactivity.
- [ ] I can write reusable plotting functions that accept an `ax`.

**Resources**

- Official Matplotlib documentation: tutorials, user guide, gallery and cheatsheets (matplotlib.org)
- *Scientific Visualization: Python + Matplotlib* by Nicolas Rougier (free online)
- *Fundamentals of Data Visualization* by Claus Wilke (free online)
- *The Visual Display of Quantitative Information* by Edward Tufte
- Next steps: seaborn for statistical charts, Plotly or Altair for interactive web charts, then the pandas and NumPy courses in this series
