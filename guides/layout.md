# Layout
There are a number of layout options available in Kumu, from fixed layouts where you position things yourself, to force-directed layouts where positions are based on relationships, to scatter plots where positions are driven by underlying attribute values.

This guide will cover the basics you need for whichever layout you choose.

## Fixed
With the fixed layout, you position everything manually. Just drag an element to change its location or drag a connection to change its curvature. The systems templates are set to a fixed layout by default.

To change to a fixed layout, use the map overview and change "Default element behavior" to "fixed". If you were previously using a floating layout, you may also need to pin all the existing elements in place. You can do this by hitting "a" to select all, and then by clicking the pin button in the bottom toolbar.

## Force-directed
With the force-directed layout, we do our best to position everything automatically based on the connections between elements. While we've picked default settings that should work for most maps, if you have an exceptionally interconnected or sparse map you may need to tweak these settings. The settings are:

- Gravity
- Element charge
- Connection length
- Connection strength

If you find your map has become a hairball, try the following:

- Decrease gravity
- Increase element charge
- Lower connection strength

The force-directed layout also allows you to override any element's position by pinning it in place.

## Scatter
With the scatter layout, elements are positioned in an XY plot using underlying attribute values. You can choose any quantitative attribute for each axis -- or even use mathematical expressions if you're up for it!

Scatter plots are in beta right now and require you to write code. If that scares you please hold off until we've tied them into the UI.

Still with us? Great! Let's dive in head first:

```
// Example scatter plot definition
//
// NOTE: These settings will only work for you if your elements
// have "influence" and "exposure" attributes as described below.
@settings {
  // Enable the scatter plot layout
  layout: scatter;
  
  // Position elements horizontally using the "influence" attribute.
  // Influence values are 0..1 and the multiplier scales them up to 0..1000.
  layout-x: "influence"; 
  layout-x-multiplier: 1000;
  
  // Position elements vertically using the "exposure" attribute.
  // Exposure values are 0..1 and the multiplier scales them up to 0..1000.
  layout-y: "exposure";
  layout-y-multiplier: 1000;
  
  // Turn on grid lines
  layout-grid: on;
  
  // Clip the layout to a 1000x1000 square
  layout-bounds-xmin: 0;
  layout-bounds-ymin: 0;
  layout-bounds-xmax: 1000;
  layout-bounds-ymax: 1000;
  
  // Use guide lines to split the square into four quadrants
  layout-guides: x(500) y(500);
}
```

You can swap out "influence" and "exposure" for the names of the attributes you'd like to use. If the values don't fall between 0 and 1 you'll need to tweak the multipliers accordingly. In general we recommend shooting for a layout that's roughly 1000x1000 but there are no hard limits if you want to get creative.

- **layout-x:** Defines the expression used for the x axis. Can be an attribute name or a mathematical expression.
- **layout-x-multiplier:** Multiplied against the result of `layout-x` to expand or contract the width of the plot.
- **layout-y:** Defines the attribute used for the y axis. Can be an attribute name or a mathematical expression.
- **layout-y-multiplier:** Multiplied against the result of `layout-y` to expand or contract the height of the plot.
- **layout-grid:** Toggle grid lines `on` or `off`.
- **layout-guides:** Defines the locations of the dominant lines on the grid. Can use `x(value)` to add a vertical guide, `y(value)` to add a horizontal guide, or `circle(radius, x, y)` to add a circle with center at `x, y`.
- **layout-bounds-*:** Defines where the grid lines should end. If not defined, they extend forever.

### Expressions

The values for `layout-x` and `layout-y` can be simple attribute expressions such as `layout-x: "degree"`. But that's not all they can be. You can also use complex mathematical expressions to build your scatter plots:

```
// NOTE: Since spaces aren't allowed in variable names, you need to use
// the camelcased version of the attribute name.
//
// eg. "levelOfInfluence * 100" instead of "level of influence * 100"
//
@settings {
  layout: scatter;
  layout-x: "1000 * betweenness";
  layout-y: "500 * max(1, log(outdegree / indegree))"
}
```

Notice there's no need to use the `-multiplier` settings if you're comfortable writing expressions instead. The rest of the scatter plot settings still apply.

We're using the [mathjs](http://mathjs.org) library under the hood so if you want to learn more we recommend checking out the following links:

- http://mathjs.org/docs/expressions/syntax.html
- http://mathjs.org/docs/reference/functions/categorical.html

<span class="edit-link"><a href="https://github.com/kumu/docs/blob/master/guides/layout.md" target="_blank"><i class="fa fa-github"></i> edit this page</a></span>