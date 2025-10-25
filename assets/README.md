# Assets

Assets are the visual building blocks of your design system - fonts, icons, images, and other media that bring your interface to life.

## Overview

This section covers how to manage, optimize, and implement assets in your design system.

## Asset Types

### [Fonts](Fonts.md)

Typography and font loading strategies.

- Font formats and loading
- System fonts vs custom fonts
- Variable fonts
- Font optimization
- Performance considerations

### [Icons](icons.md)

Icon systems and implementations.

- SVG vs icon fonts
- Icon accessibility
- Icon libraries
- Custom icons
- Optimization strategies

### [Images](images.md)

Image handling and optimization.

- Image formats
- Responsive images
- Lazy loading
- Image optimization
- Accessibility

## Asset Management

### Organization

```
assets/
├── fonts/
│   ├── inter-regular.woff2
│   └── inter-bold.woff2
├── icons/
│   ├── home.svg
│   └── search.svg
└── images/
    ├── hero.jpg
    └── patterns/
```

### Naming Conventions

- **Fonts**: `font-name-weight.woff2` (e.g., `inter-regular.woff2`)
- **Icons**: `kebab-case.svg` (e.g., `arrow-right.svg`)
- **Images**: `kebab-case-version.jpg` (e.g., `hero-1x.jpg`)

## Best Practices

1. **Optimize everything** - Compress fonts, images, and SVG files
2. **Modern formats first** - Use WOFF2, WebP, AVIF where supported
3. **Responsive by default** - Serve appropriate sizes for each device
4. **Performance budget** - Set limits on file sizes
5. **CDN delivery** - Serve assets from CDN for speed
6. **Version control** - Use meaningful filenames and cache busting

## Performance Targets

- **Fonts**: < 100KB total
- **Icons**: < 50KB for icon set
- **Images**: < 200KB per image
- **First load**: < 500KB total assets

## Accessibility

- Always provide alt text for images
- Use semantic HTML for icons
- Ensure sufficient contrast for text
- Test with screen readers

## Resources

- [Asset Pipeline Best Practices](performance.md)
- [Accessibility Guide](../Accessibility/README.md)
- [Components](../Components/README.md) - How components use assets
