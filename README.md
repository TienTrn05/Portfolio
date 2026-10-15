# Trần Ngọc Tiến — Portfolio

A Full-stack Developer portfolio built with React 19, Vite and Tailwind CSS 4. It presents three featured projects by default, an expandable project archive, capability evidence, case studies and contact options.

## Documentation

- [Mô tả ngắn dùng trong CV (Tiếng Việt)](docs/CV_SUMMARY_VI.md)
- [Tài liệu kỹ thuật: kiến trúc, hook, thuật toán và thư viện (Tiếng Việt)](docs/PORTFOLIO_TECHNICAL_GUIDE_VI.md)

## Run locally

```bash
npm install
npm run dev
```

```bash
npm run lint
npm run format:check
npm run build
```

## Structure

```text
src/
  components/
    layout/       Header, display settings and contact footer
    overlays/     Case study dialog
    sections/     Page sections
    ui/           Reusable icons and tags
  features/
    projects/     Project data, cards and visual evidence
  config/         Navigation and portfolio links
  hooks/          Intersection reveals and page effects
  styles/         Theme tokens and small global motion rules
```

Page layouts and components use Tailwind utilities. Semantic colors come from `src/styles/tokens.css`; see [THEME_SYSTEM.md](THEME_SYSTEM.md). The workflow logos are local Devicon SVGs with their license at `public/assets/icons/brands/LICENSE.txt`.

The motion toggle respects `prefers-reduced-motion` on first use. An explicit setting is stored locally. The contact form submits to FormSubmit, while email, phone and social links remain available directly.
