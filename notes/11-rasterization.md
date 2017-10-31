[← Return to Index](https://github.com/cjmlgrto/fit3088-notes/)

# Rasterization

**Rasterization** is the process of converting points to discrete pixels.

## Digital Differential Analyzer (DDA) Algorithm

* The simplest **scan-conversion algorithm** for line segments
* `∆y = m∆x` → `∆y = m`

```java
for (ix=x1; ix <= x2; ix++) {
	y += m;
	write_pixel(x, round(y), line_color);
}
```

## Bresenham's Line Algorithm

```python
plotLine(x0,y0, x1,y1)
  dx = x1 - x0
  dy = y1 - y0
  D = 2*dy - dx
  y = y0

  for x from x0 to x1
    plot(x,y)
    if D > 0
       y = y + 1
       D = D - 2*dx
    end if
    D = D + 2*dy
```
