# Plotly Chart Chooser

A short guide to picking a Plotly chart based on the type of data and the comparison you want to make.

```python
import plotly.express as px
import plotly.graph_objects as go
import plotly.figure_factory as ff
```

## Quick lookup

| I want to see | Chart | Plotly call |
|---|---|---|
| Distribution of one numeric variable | Histogram | `px.histogram` |
| Smooth shape of a distribution | KDE | `ff.create_distplot` |
| Median, quartiles, outliers | Box | `px.box` |
| Shape and summary stats together | Violin | `px.violin` |
| Every raw point | Strip | `px.strip` |
| Percentiles | ECDF | `px.ecdf` |
| Counts of a category | Bar | `px.bar` |
| Two numerics | Scatter | `px.scatter` |
| Two numerics, very large data | Density heatmap | `px.density_heatmap` |
| Trend over time | Line | `px.line` |
| Part of a whole | Treemap / Sunburst / Pie | `px.treemap`, `px.sunburst`, `px.pie` |
| Correlation of many numerics | Heatmap | `px.imshow` |
| Pairwise relationships | Scatter matrix | `px.scatter_matrix` |
| Flow between stages | Sankey | `go.Sankey` |
| Values on a map | Choropleth | `px.choropleth` |

## One numeric variable

| Chart | Use when |
|---|---|
| Histogram | First look at a column: shape, skew, gaps |
| KDE | Smooth shape, or overlaying a few groups |
| Box | Quick median, spread and outliers |
| Violin | Shape and quartiles together, mostly for comparing groups |
| Strip | Small data, show every point |
| ECDF | Percentiles, comparing groups without choosing bins |

```python
px.histogram(df, x="age", nbins=30, marginal="box")
px.violin(df, y="age", box=True, points="all")
px.ecdf(df, x="age")

# KDE (needs scipy)
ff.create_distplot([df["age"]], ["age"], show_hist=False)
```

Histogram shows counts and is the easiest to explain. KDE is a smoothed version of it. A violin is a KDE mirrored on both sides, best when comparing a numeric across categories.

## One categorical variable

Use a sorted bar chart. Use a pie or donut only when there are 5 or fewer slices.

```python
counts = df["city"].value_counts().reset_index()
px.bar(counts, x="city", y="count")
```

## Numeric vs numeric

| Chart | Use when |
|---|---|
| Scatter | Relationship, clusters, outliers |
| Scatter with trendline | Show the trend (`trendline="ols"`) |
| Bubble | Add a third numeric as `size` |
| Density heatmap / contour | Too many points, overplotting |

```python
px.scatter(df, x="height", y="weight", color="gender", trendline="ols")
px.density_heatmap(df, x="height", y="weight")
```

## Numeric across categories

| Chart | Use when |
|---|---|
| Box | Compare medians and spread across many groups |
| Violin | Compare full shapes across groups |
| Strip | Few points per group |
| Overlaid histogram or KDE | Two or three groups |

```python
px.box(df, x="species", y="sepal_length")
px.violin(df, x="day", y="tip", color="sex", box=True)
```

A bar of group means hides spread and outliers. Prefer box or violin.

## Categorical vs categorical

| Chart | Use when |
|---|---|
| Grouped bar | Compare sub-categories side by side |
| Stacked bar | Totals and composition |
| 100% stacked bar | Compare proportions, not counts |
| Heatmap of a crosstab | Many categories on both axes |

```python
px.bar(df, x="day", color="sex", barmode="group")
px.imshow(pd.crosstab(df["day"], df["time"]), text_auto=True)
```

## Time series

| Chart | Use when |
|---|---|
| Line | Default for trends |
| Area | Magnitude or composition over time |
| Bar | Discrete periods such as monthly totals |
| Candlestick | Financial prices |

```python
fig = px.line(df, x="date", y="value", color="group")
fig.update_xaxes(rangeslider_visible=True)
```

## Three or more variables

| Chart | Use when |
|---|---|
| Scatter matrix | Pairwise look at 3 to 8 numerics |
| Correlation heatmap | Overview of many numerics |
| Parallel coordinates | Compare many numeric columns per row |
| Facets | Split any chart by a category |

```python
px.scatter_matrix(df, dimensions=["a", "b", "c"], color="label")
px.imshow(df.corr(numeric_only=True), text_auto=".2f", zmin=-1, zmax=1)
px.scatter(df, x="a", y="b", facet_col="group")
```

## Part of a whole

| Chart | Use when |
|---|---|
| Pie / donut | 5 or fewer categories |
| Treemap | Many categories or a hierarchy |
| Sunburst | Hierarchy with parent-child nesting |
| Funnel | Drop-off across stages |
| Waterfall | Start value plus increases and decreases |

## Maps

| Chart | Use when |
|---|---|
| Choropleth | One value per country or region |
| Scatter map | Points with latitude and longitude |
| Density map | Many points, show concentration |

```python
px.choropleth(df, locations="iso_alpha", color="gdp")
px.scatter_map(df, lat="lat", lon="lon", size="count")
```

## Decision flow

```
1 variable
  numeric        histogram, KDE, box, violin, ECDF
  categorical    bar

2 variables
  num + num      scatter
  num + cat      box, violin, strip
  cat + cat      grouped or stacked bar, heatmap
  time + num     line, area

3 or more variables
  add color, size or facets to a 2-variable chart
  many numerics  scatter matrix, correlation heatmap
  geography      choropleth, scatter map
```

## Common mistakes

| Avoid | Prefer |
|---|---|
| Pie chart with many slices | Sorted horizontal bar |
| Bar of means with no spread | Box or violin |
| 3D charts for 2D data | Color, size or facets |
| Scatter with 100k+ points | `density_heatmap` or `render_mode="webgl"` |
| Bar chart axis not starting at zero | Start bars at zero |
| Raw counts on a choropleth | Normalize per capita or per area |

Docs: https://plotly.com/python/
