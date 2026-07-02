# HTML Briefing Schema Specification

This document defines the structure for the final HTML slide deck. The output must follow these exact specifications to render correctly in the viewer.

## Slide Structure

The deck requires 5-7 slides. Each slide needs a specific ID, title, and content type.

| Slide ID | Title | Content Type | Media Requirements |
|---|---|---|---|
| `slide-1` | Executive Summary | Text | Optional hero image |
| `slide-2` | Key Findings | List | None |
| `slide-3` | Data Analysis | Chart | Requires one SVG or Canvas element |
| `slide-4` | Expert Quotes | Quote | Author headshot if available |
| `slide-5` | Limitations | List | None |
| `slide-6` | References | List | None |
| `slide-7` | Appendix / Next Steps | List | Optional closing notes |

Slides 1-5 are required. Slides 6-7 are optional and may be omitted when the topic does not need extra reference or appendix content.

## Responsive Design Constraints

Build the layout with a mobile first approach. The base breakpoint is 375px. 

Scale typography and spacing up for larger screens using CSS clamp functions. Do not use fixed pixel widths for containers. Images must have a max width of 100% to prevent horizontal scrolling on small devices.

## CSS Naming Convention

Use a strict prefix for all classes to avoid conflicts. The prefix is `storm-`.

*   Containers: `storm-slide-container`
*   Typography: `storm-heading`, `storm-body`, `storm-caption`
*   Interactive elements: `storm-button`, `storm-link`

## Accessibility Requirements

Every visual element needs proper context for screen readers. 

You must include descriptive alt text for all images. Decorative images need an empty alt attribute. Use ARIA labels for any interactive elements that lack clear text content. 

Color contrast must pass WCAG AA standards. This means a contrast ratio of at least 4.5 to 1 for normal text and 3 to 1 for large text. Test your color palette before finalizing the CSS.
