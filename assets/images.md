# Images

Images are a crucial part of modern web applications. Properly optimized and implemented images improve performance, accessibility, and user experience.

## Image Formats

### Modern Formats

#### WebP

Best compression and quality balance.

```html
<picture>
  <source srcset="image.webp" type="image/webp">
  <img src="image.jpg" alt="Description">
</picture>
```

#### AVIF

Next-generation format, excellent compression.

```html
<picture>
  <source srcset="image.avif" type="image/avif">
  <source srcset="image.webp" type="image/webp">
  <img src="image.jpg" alt="Description">
</picture>
```

#### Legacy Formats

- **JPEG**: Photos, complex images
- **PNG**: Graphics with transparency
- **SVG**: Scalable vector graphics

## Responsive Images

### Srcset

Serve different sizes for different screen sizes.

```html
<img 
  srcset="image-320w.jpg 320w,
          image-640w.jpg 640w,
          image-1024w.jpg 1024w"
  sizes="(max-width: 640px) 320px,
         (max-width: 1024px) 640px,
         1024px"
  src="image-640w.jpg"
  alt="Description"
/>
```

### Picture Element

Different sources for different conditions.

```html
<picture>
  <source 
    media="(min-width: 1024px)"
    srcset="desktop-image.jpg">
  <source 
    media="(min-width: 640px)"
    srcset="tablet-image.jpg">
  <img 
    src="mobile-image.jpg" 
    alt="Description"
  >
</picture>
```

## Image Optimization

### Before Upload

1. **Resize** to actual display size
2. **Compress** using tools like TinyPNG
3. **Choose format** (WebP for photos, SVG for graphics)
4. **Optimize** with automated tools

### Compression Tools

- ImageOptim (Mac)
- Squoosh (Web)
- TinyPNG (Web)
- Sharp (Node.js)

### Sharp Example

```typescript
import sharp from 'sharp';

async function optimizeImage(input: string, output: string) {
  await sharp(input)
    .resize(1920, 1080, { fit: 'inside' })
    .webp({ quality: 80 })
    .toFile(output);
}
```

## Lazy Loading

### Native Lazy Loading

```html
<img 
  src="image.jpg" 
  alt="Description"
  loading="lazy"
/>
```

### Intersection Observer

```tsx
function LazyImage({ src, alt }: ImageProps) {
  const imgRef = useRef<HTMLImageElement>(null);
  const [inView, setInView] = useState(false);
  
  useEffect(() => {
    const observer = new IntersectionObserver(
      ([entry]) => setInView(entry.isIntersecting)
    );
    
    if (imgRef.current) {
      observer.observe(imgRef.current);
    }
    
    return () => observer.disconnect();
  }, []);
  
  return (
    <img
      ref={imgRef}
      src={inView ? src : undefined}
      alt={alt}
      loading="lazy"
    />
  );
}
```

## Placeholder Images

### Solid Color

```html
<img 
  src="image.jpg"
  style="background-color: #f0f0f0;"
  alt="Description"
/>
```

### Blur Placeholder

```html
<img 
  src="image.jpg"
  style="background-image: url('blur-placeholder.jpg');"
  alt="Description"
/>
```

### Low-Quality Image Placeholder (LQIP)

```html
<img 
  src="image.jpg"
  srcset="lqip.jpg 100w, image.jpg 800w"
  sizes="100px"
  alt="Description"
/>
```

## Accessibility

### Alt Text

```html
<!-- ✅ Good: Descriptive -->
<img src="chart.jpg" alt="Sales increased by 23% in Q3 2024" />

<!-- ❌ Bad: Non-descriptive -->
<img src="chart.jpg" alt="Chart" />

<!-- ✅ Good: Decorative -->
<img src="decorative.jpg" alt="" role="presentation" />

<!-- ✅ Good: Complex content -->
<img src="infographic.jpg" alt="See detailed description below">
<p id="image-desc">[Detailed description of infographic]</p>
```

### Proper Elements

```html
<!-- ✅ Good: Visual content -->
<img src="photo.jpg" alt="Team photo from company retreat" />

<!-- ✅ Good: Information display -->
<img src="chart.png" alt="Revenue chart showing 20% growth" />

<!-- ❌ Bad: Don't use img for text -->
<img src="heading.jpg" alt="Welcome to our site" />
<!-- Use real text instead -->
<h1>Welcome to our site</h1>
```

## Image Loading States

### Skeleton

```tsx
function ImageWithSkeleton({ src, alt }: ImageProps) {
  const [loading, setLoading] = useState(true);
  
  return (
    <div className="relative">
      {loading && (
        <div className="absolute inset-0 bg-gray-200 animate-pulse" />
      )}
      <img
        src={src}
        alt={alt}
        onLoad={() => setLoading(false)}
        className={loading ? 'opacity-0' : 'opacity-100'}
      />
    </div>
  );
}
```

### Progressive Enhancement

```tsx
function ProgressiveImage({ src, alt }: ImageProps) {
  const [imageSrc, setImageSrc] = useState(src);
  const [loaded, setLoaded] = useState(false);
  
  return (
    <div>
      <img
        src={imageSrc}
        alt={alt}
        onLoad={() => setLoaded(true)}
        className={loaded ? 'fade-in' : ''}
      />
    </div>
  );
}
```

## Image Handling Best Practices

### CDN Usage

Serve images from CDN for better performance.

```tsx
function Image({ src, alt }: ImageProps) {
  const cdnUrl = 'https://cdn.example.com';
  const fullSrc = src.startsWith('http') ? src : `${cdnUrl}${src}`;
  
  return <img src={fullSrc} alt={alt} />;
}
```

### Responsive Width

```css
img {
  max-width: 100%;
  height: auto;
}
```

### Aspect Ratio

```html
<img 
  src="image.jpg" 
  alt="Description"
  style="aspect-ratio: 16 / 9;"
/>
```

```css
.image-container {
  aspect-ratio: 16 / 9;
  overflow: hidden;
}

.image-container img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}
```

## Art Direction

### Picture Element for Art Direction

```html
<picture>
  <source 
    media="(min-width: 1024px)"
    srcset="wide-cropped.jpg"
  >
  <img 
    src="tall-cropped.jpg" 
    alt="Team photo"
  >
</picture>
```

## Performance Optimization

### Image Preloading

```html
<link rel="preload" as="image" href="hero-image.jpg">
```

### Critical Images

Load critical images first.

```html
<!-- Critical hero image -->
<img src="hero.jpg" fetchpriority="high" alt="Hero">

<!-- Non-critical images -->
<img src="gallery-1.jpg" loading="lazy" alt="Gallery image 1">
```

### Image Dimensions

Always specify width and height to prevent layout shift.

```html
<img 
  src="image.jpg" 
  alt="Description"
  width="800"
  height="600"
/>
```

## Security

### File Upload

```typescript
function validateImage(file: File): boolean {
  // Check file type
  if (!file.type.startsWith('image/')) {
    return false;
  }
  
  // Check file size (max 5MB)
  if (file.size > 5 * 1024 * 1024) {
    return false;
  }
  
  return true;
}
```

### Sanitization

Never trust user-uploaded images. Always sanitize and validate on the server.

## Common Patterns

### Avatar

```tsx
function Avatar({ src, alt, size = 40 }: AvatarProps) {
  return (
    <img
      src={src}
      alt={alt}
      width={size}
      height={size}
      className="rounded-full"
    />
  );
}
```

### Image Grid

```tsx
function ImageGrid({ images }: { images: string[] }) {
  return (
    <div className="grid grid-cols-3 gap-4">
      {images.map((src, i) => (
        <img
          key={i}
          src={src}
          alt={`Image ${i + 1}`}
          loading="lazy"
          className="w-full h-full object-cover"
        />
      ))}
    </div>
  );
}
```

### Background Image

```css
.hero {
  background-image: url('hero.jpg');
  background-size: cover;
  background-position: center;
}

/* Optimized */
.hero {
  background-image: image-set(
    'hero.avif' type('image/avif'),
    'hero.webp' type('image/webp'),
    'hero.jpg' type('image/jpeg')
  );
}
```

## Best Practices

1. **Optimize before upload** - Resize and compress
2. **Use modern formats** - WebP, AVIF
3. **Implement lazy loading** - Native or JavaScript
4. **Provide alt text** - Always descriptive
5. **Specify dimensions** - Prevent layout shift
6. **Responsive images** - Use srcset and sizes
7. **CDN delivery** - Faster loading
8. **Progressive enhancement** - Load critical images first

## Resources

- [Squoosh](https://squoosh.app/) - Image compressor
- [Cloudinary](https://cloudinary.com/) - Image CDN
- [Images.engineering](https://images.guide/) - Image optimization guide
- [MDN: Responsive Images](https://developer.mozilla.org/en-US/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images)

## Next Steps

- [Assets Overview](README.md) - Asset management
- [Performance](performance.md) - Image loading performance
- [Components](Components/README.md) - Image components

