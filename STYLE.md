# Style Guide — PrestaShop Base

Quy tắc CSS/SCSS chính thức cho `Cuongle99/Prestashop-base`.

Mục tiêu: code giao diện nhất quán, dễ mở rộng, dễ maintain, responsive, accessible, tối ưu hiệu năng và hạn chế CSS/JS không cần thiết.

## 1. Nguyên tắc tổng quát

- Ưu tiên semantic HTML và CSS chuẩn trước khi dùng JavaScript.
- CSS phải có phạm vi rõ ràng, tránh selector global gây side effect.
- Ưu tiên BEM cho component/class tùy biến.
- Ưu tiên SCSS có cấu trúc rõ ràng thay vì viết CSS rời rạc.
- Không sửa trực tiếp vendor/Bootstrap nếu có thể override bằng layer riêng.
- Không dùng `!important` trừ trường hợp thật sự cần thiết hoặc phải override code bên thứ ba.
- Không tạo nhiều selector khác nhau cho cùng một mục đích.
- Không copy/paste cùng một nhóm style giữa nhiều component; dùng mixin/function/utility phù hợp.
- Mọi UI mới phải hoạt động tốt trên mobile, tablet và desktop.

## 2. Kiến trúc CSS/SCSS

Base hiện có cấu trúc `_dev/css`, gồm variables, Bootstrap và các style ứng dụng. Khi thêm code mới, ưu tiên giữ separation giữa foundation và component.

Thứ tự ưu tiên:

1. Settings / variables
2. Tools / functions / mixins
3. Generic / reset / normalize
4. Elements
5. Objects / layout
6. Components
7. Utilities / helpers
8. Overrides / vendor-specific fixes

Không để component phụ thuộc ngược vào component khác nếu không cần thiết.

## 3. Naming — BEM

Sử dụng:

- Block: `.product-card`
- Element: `.product-card__image`
- Modifier: `.product-card--featured`
- Element modifier: `.product-card__button--primary`

Ví dụ:

```html
<article class="product-card product-card--featured">
  <div class="product-card__media">
    <img class="product-card__image" src="..." alt="...">
  </div>
  <div class="product-card__content">
    <h3 class="product-card__title">Product name</h3>
    <a class="product-card__link" href="...">View product</a>
  </div>
</article>
```

### Quy tắc

- Class dùng chữ thường, nhiều từ nối bằng `-`.
- Element dùng `__`.
- Modifier dùng `--`.
- Không tạo BEM lồng nhiều cấp như `.product-card__content__title`.
- Nếu một phần tử là component độc lập, tạo block mới thay vì tiếp tục nesting.
- Không đặt tên theo hình thức/visual nếu tên semantic có thể sử dụng.
- Tránh các class mơ hồ như `.box`, `.item`, `.left`, `.red`, `.big`.

## 4. Selector

Ưu tiên selector class đơn giản:

```scss
.product-card {
  ...
}

.product-card__title {
  ...
}
```

Hạn chế:

```scss
#content .product-list .product-card div span {
  ...
}
```

### Quy tắc selector

- Ưu tiên class selector.
- Hạn chế ID selector.
- Hạn chế selector theo tag khi không thực sự cần.
- Hạn chế descendant selector sâu hơn 2–3 cấp.
- Không style dựa trên vị trí DOM nếu có thể dùng class semantic.
- Tránh selector `:nth-child()` cho layout component nếu class có thể giải quyết rõ ràng hơn.

## 5. SCSS nesting

Nesting tối đa khoảng 2–3 cấp và phải dễ đọc.

Tốt:

```scss
.product-card {
  display: flex;

  &__title {
    margin: 0;
  }

  &:hover {
    ...
  }
}
```

Không nên:

```scss
.product-card {
  .product-card__content {
    .product-card__title {
      a {
        span {
          ...
        }
      }
    }
  }
}
```

Pseudo-class/pseudo-element nên đặt trong block tương ứng:

```scss
.button {
  ...

  &:hover { ... }
  &:focus-visible { ... }
  &:disabled { ... }
}
```

## 6. Variables và Design Tokens

Tất cả giá trị dùng nhiều lần phải được tập trung thành variables.

Ưu tiên CSS custom properties cho design tokens runtime:

```scss
:root {
  --color-primary: #1f3a8a;
  --color-text: #1f2937;
  --container-width: 1440px;
  --space-md: 1rem;
  --radius-md: 8px;
}
```

SCSS variables dùng cho compile-time configuration:

```scss
$breakpoint-md: 768px;
$breakpoint-lg: 1024px;
```

Không hard-code cùng một màu, spacing hoặc breakpoint ở nhiều file.

## 7. Responsive

Mobile-first là mặc định.

```scss
.product-grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: clamp(16px, 2vw, 32px);

  @media (min-width: 768px) {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }

  @media (min-width: 1024px) {
    grid-template-columns: repeat(4, minmax(0, 1fr));
  }
}
```

Ưu tiên `clamp()`, `min()`, `max()`, `minmax()` và fluid sizing khi phù hợp thay vì tạo quá nhiều breakpoint.

Không dùng breakpoint chỉ để sửa một lỗi spacing nhỏ nếu có thể giải quyết bằng layout fluid.

## 8. Layout

Ưu tiên:

1. CSS Grid cho page/grid layout.
2. Flexbox cho alignment và component layout.
3. Container + `max-width` để kiểm soát content width.
4. `gap` thay cho margin giữa các item khi phù hợp.

Ví dụ:

```scss
.container {
  width: min(100% - 32px, var(--container-width));
  margin-inline: auto;
}
```

Tránh layout bằng `float`, negative margin hoặc absolute positioning nếu Grid/Flexbox giải quyết được.

## 9. Spacing

- Ưu tiên hệ spacing nhất quán.
- Dùng `gap` cho khoảng cách giữa children.
- Dùng logical properties khi phù hợp: `margin-inline`, `padding-inline`, `inset-inline`.
- Không tạo spacing ngẫu nhiên cho từng component.
- Với responsive spacing, ưu tiên `clamp()`.

Ví dụ:

```scss
.section {
  padding-block: clamp(32px, 5vw, 80px);
}
```

## 10. Typography

- Typography phải có hierarchy rõ ràng.
- Không set font-size tùy tiện trong từng component.
- Ưu tiên token cho font family, size, weight và line-height.
- Heading phải phù hợp semantic HTML (`h1` → `h6`).
- Không dùng CSS để thay thế semantic heading.

```scss
.heading-xl {
  font-size: clamp(2rem, 4vw, 4rem);
  line-height: 1.1;
}
```

## 11. Colors

Không hard-code màu lặp lại.

Tốt:

```scss
color: var(--color-text);
background: var(--color-surface);
border-color: var(--color-border);
```

Không nên:

```scss
color: #1f2937;
color: #1f2937;
color: #1f2937;
```

Màu trạng thái phải có token riêng: success, warning, danger, info, disabled, focus.

## 12. States

Component tương tác phải định nghĩa tối thiểu khi cần:

- default
- hover
- focus-visible
- active
- disabled
- loading
- selected/current
- error

Không được chỉ dựa vào `:hover` cho hành vi quan trọng vì touch device không có hover.

## 13. Accessibility

- Không dùng màu làm tín hiệu duy nhất.
- Focus state phải luôn nhìn thấy được.
- Không dùng `outline: none` nếu không thay thế bằng focus indicator rõ ràng.
- Đảm bảo contrast phù hợp.
- Button/link phải có trạng thái disabled/loading rõ ràng.
- Không chặn keyboard interaction bằng CSS/JS.
- Không dùng pseudo-element để chứa nội dung quan trọng.
- Nội dung có ý nghĩa phải nằm trong HTML semantic.

Ví dụ:

```scss
.button:focus-visible {
  outline: 2px solid currentColor;
  outline-offset: 3px;
}
```

## 14. Reduced Motion

Animation không được gây khó chịu hoặc ảnh hưởng người dùng nhạy cảm với chuyển động.

```scss
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    scroll-behavior: auto !important;
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

## 15. Animation và Transition

- Chỉ animate `transform` và `opacity` khi có thể.
- Hạn chế animate `width`, `height`, `top`, `left` vì có thể gây layout/reflow.
- Transition phải ngắn, có mục đích và nhất quán.
- Không animation mọi element chỉ để tạo hiệu ứng.

```scss
.card {
  transition: transform 180ms ease, opacity 180ms ease;
}
```

## 16. Images và Media

- Hình ảnh phải có `alt` phù hợp trong HTML.
- Không dùng CSS background image cho nội dung có ý nghĩa.
- Dùng `object-fit` và `aspect-ratio` để kiểm soát media layout.
- Tránh layout shift bằng cách xác định kích thước/aspect ratio.

```scss
.product-card__media {
  aspect-ratio: 1 / 1;
  overflow: hidden;
}

.product-card__image {
  width: 100%;
  height: 100%;
  object-fit: cover;
}
```

## 17. Performance

- Không tạo selector quá phức tạp.
- Hạn chế CSS duplicate.
- Không import toàn bộ framework chỉ để dùng một vài component nếu build pipeline cho phép loại bỏ phần không dùng.
- Không tạo animation chạy liên tục nếu không cần thiết.
- Hạn chế `box-shadow`, filter và blur nặng trên số lượng lớn element.
- Không dùng CSS/JS để tạo UI có thể giải quyết bằng HTML/CSS native.
- CSS critical cho above-the-fold phải được ưu tiên khi build system hỗ trợ.

## 18. Bootstrap / Vendor

Repository có thư mục `_dev/css/bootstrap`. Bootstrap/vendor code được xem là dependency/foundation.

Quy tắc:

- Không sửa vendor code chỉ để thay đổi một component của project.
- Override ở layer application.
- Không phụ thuộc quá sâu vào DOM structure nội bộ của Bootstrap.
- Khi tạo component mới, ưu tiên API/style riêng của base thay vì kéo thêm dependency.

## 19. PrestaShop-specific

### Theme templates

- Smarty template chịu trách nhiệm markup/data presentation.
- SCSS chịu trách nhiệm presentation.
- JavaScript chịu trách nhiệm interaction/behavior.
- Không đưa CSS inline vào `.tpl` nếu không có lý do rõ ràng.
- Không đưa business logic phức tạp vào template.

### Module markup

Khi style markup của module bên thứ ba:

- Ưu tiên scope selector trong component/page wrapper.
- Không override class global nếu không cần.
- Nếu module có class riêng, giữ class module và tạo wrapper/project class khi cần.

Ví dụ:

```scss
.product-page {
  .third-party-module {
    ...
  }
}
```

Tốt hơn:

```scss
.product-page__reviews {
  ...
}
```

## 20. Order properties

Trong một rule SCSS, ưu tiên thứ tự:

1. Layout / positioning
2. Box model
3. Typography
4. Visual
5. Effects / animation
6. States

Ví dụ:

```scss
.product-card {
  position: relative;
  display: flex;
  gap: 16px;
  width: 100%;
  padding: 16px;

  font-size: 1rem;
  line-height: 1.5;

  color: var(--color-text);
  background: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-md);

  transition: transform 180ms ease;
}
```

## 21. Comments

Comment chỉ dùng khi giải thích **vì sao**, không dùng để mô tả điều quá hiển nhiên.

Tốt:

```scss
// Keep this offset because the sticky header overlaps the anchor target.
```

Không cần:

```scss
// Set margin to 20px
margin: 20px;
```

## 22. Utilities

Utility class chỉ nên dùng cho các pattern thực sự generic và có tính tái sử dụng cao.

Ví dụ:

```scss
.u-visually-hidden { ... }
.u-text-center { ... }
.u-hidden { ... }
```

Không tạo utility cho từng giá trị riêng lẻ của một component.

## 23. Không làm

Không nên:

```scss
#content .row .col-md-6 .product .title span {
  margin-left: 17px !important;
}
```

Nên:

```scss
.product-card__title {
  margin-inline-start: var(--space-sm);
}
```

Không nên tạo class theo page-specific nếu component có thể tái sử dụng:

```scss
.home-product-title { ... }
```

Ưu tiên:

```scss
.product-card__title { ... }
```

## 24. Checklist trước khi commit

- [ ] Class mới tuân thủ BEM hoặc là utility class hợp lệ.
- [ ] Không có selector quá sâu.
- [ ] Không có `!important` không cần thiết.
- [ ] Không duplicate màu/spacing/breakpoint đã có token.
- [ ] Responsive tốt ở mobile/tablet/desktop.
- [ ] Có `:focus-visible` cho component tương tác.
- [ ] Không phá layout hoặc style của component khác.
- [ ] Không thêm dependency chỉ cho một UI nhỏ nếu CSS/JS native đủ.
- [ ] Animation có mục đích và hỗ trợ `prefers-reduced-motion` khi cần.
- [ ] SCSS nesting vẫn dễ đọc.
- [ ] Không có CSS inline nếu không cần thiết.
- [ ] Không sửa trực tiếp vendor/Bootstrap khi có thể override.

## 25. Quy tắc ưu tiên

Khi có xung đột giữa các quy tắc, ưu tiên theo thứ tự:

1. Accessibility
2. Correct semantic HTML
3. Maintainability
4. Performance
5. Responsive behavior
6. Consistency with existing design tokens
7. Visual preference

**Nguyên tắc cuối cùng:** code mới phải làm cho base dễ mở rộng hơn, không chỉ làm cho một màn hình hiện tại trông đúng.
