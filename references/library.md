# FauxDS Library Reference

> Last generated: 2026-03-26T00:00:00Z
> Source paths: design/prototype-playground/src/components/design-system, design/prototype-playground/src/styles/tokens, design/prototype-playground/src/components/icons
> Components: 35 | Tokens: 285 | Icons: 85

---

## Global Theme

```css
/* Font */
--slate-typography-family-sans-serif: "Graphik", -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
--slate-typography-family-serif: "Nantes_fix", Georgia, serif;

/* Text */
--slate-color-text-primary: #333333;       /* grey-900 */
--slate-color-text-subdued: #757575;       /* grey-700 */
--slate-color-text-primary-inverse: #ffffff; /* grey-100 */

/* Borders */
--slate-color-border-muted: #dfe0e1;       /* grey-400 */
--slate-color-border-subdued: #757575;     /* grey-700 */

/* Surfaces */
--slate-color-surface-primary: #ffffff;           /* grey-100 */
--slate-color-surface-secondary: #fbfbfb;         /* grey-200 */
--slate-color-surface-tertiary: #f7f7f7;          /* grey-300 */
--slate-color-surface-mask: rgba(51,51,51,0.5);   /* transparentgrey-500 */
--slate-color-surface-overlay: rgba(51,51,51,0.05); /* transparentgrey-100 */
--slate-color-surface-primary-inverse: #333333;   /* grey-900 */

/* Radii */
--slate-radius-component-default: 4px;     /* dimensions-1 */
--slate-radius-component-round: 999px;
--slate-radius-component-checkbox: 2px;
--slate-radius-component-tag: 2px;
--slate-radius-container-default: 4px;     /* dimensions-1 */

/* Focus Ring */
--slate-color-action-border-focus-ring: #275ec5; /* blue-700 */

/* Component Sizes */
--slate-button-height-default: 48px;       /* dimensions-12 */
--slate-button-height-small: 40px;         /* dimensions-10 */
--slate-button-padding-x: 24px;            /* dimensions-6 */
--slate-button-padding-x-small: 16px;      /* dimensions-4 */
--slate-input-height: 48px;                /* dimensions-12 */
--slate-input-padding-x: 16px;             /* dimensions-4 */
--slate-input-border-width: 1px;
--slate-checkbox-size: 20px;               /* dimensions-5 */
--slate-modal-padding: 24px;               /* dimensions-6 */
--slate-modal-max-width: 560px;
--slate-max-width-content-narrow: 960px;
```

---

## Components

## Button

Import: `import { Button } from '@/components/design-system'`
Variants: primary, secondary, tertiary, plain
Sizes: xSmall, small, medium
Props:
  - children: React.ReactNode (required)
  - variant: "primary" | "secondary" | "tertiary" | "plain" (default: "primary")
  - destructive: boolean (default: false, uses critical/red color scheme)
  - size: "xSmall" | "small" | "medium" (default: "medium")
  - disabled: boolean (default: false)
  - loading: boolean (default: false)
  - loadingLabel: boolean (default: false, shows "Loading label" text)
  - icon: React.ReactNode (optional)
  - iconPosition: "start" | "end" (default: "end")
  - onClick: (event: React.MouseEvent) => void (optional)
  - type: "button" | "submit" | "reset" (default: "button")
  - style: React.CSSProperties (optional)
  - className: string (optional)
States: hover (bg darkens), active (same as hover), disabled (bg #f7f7f7, text #757575, cursor-not-allowed), loading (spinner, hides icon, aria-busy), focus-visible (ring-2)
Gotchas: Plain variant has auto height, no padding, adds underline. Tertiary uses mixBlendMode: "multiply". Loading disables button. xSmall uses font-size-100/line-height-100; small/medium use 200-level. forwardRef.
Usage: `<Button variant="secondary" size="small" onClick={fn}>Click me</Button>`

---

## TextInput

Import: `import { TextInput } from '@/components/design-system'`
Variants: none
Sizes: none (fixed 40px height)
Props:
  - label: string (optional)
  - placeholder: string (optional)
  - value: string (optional, controlled)
  - defaultValue: string (optional, uncontrolled)
  - onChange: (event: React.ChangeEvent<HTMLInputElement>) => void (optional)
  - disabled: boolean (default: false)
  - error: boolean | string (optional, boolean for state, string for message)
  - helper: string (optional)
  - type: "text" | "email" | "password" | "search" | "tel" | "url" | "number" (default: "text")
  - name: string (optional)
  - required: boolean (default: false, shows red asterisk)
  - className: string (optional)
States: disabled (bg #f7f7f7, border #dfe0e1, text #757575), error (border #d17e70, label #921100), focus (outline #000000 or #d17e70 in error)
Gotchas: Error can be boolean (border only) or string (replaces helper text with error message). Uses hardcoded hex values. forwardRef.
Usage: `<TextInput label="Email" placeholder="Enter email" error="Invalid email" onChange={fn} />`

---

## Checkbox

Import: `import { Checkbox } from '@/components/design-system'`
Variants: none
Sizes: none (fixed 16x16px)
Props:
  - checked: boolean | "indeterminate" (default: false)
  - defaultChecked: boolean (optional, uncontrolled)
  - onChange: (event: React.ChangeEvent<HTMLInputElement>) => void (optional)
  - disabled: boolean (default: false)
  - label: string (optional)
  - name: string (optional)
  - value: string (optional)
  - className: string (optional)
States: checked (border #000, bg #000, white checkmark), indeterminate (border #000, bg #000, white minus, aria-checked="mixed"), unchecked (border #333, transparent), disabled (border #dfe0e1, bg #f7f7f7, cursor-not-allowed), focus-visible (ring)
Gotchas: "indeterminate" is a string value for checked prop, not a separate prop. forwardRef (ref goes to hidden input).
Usage: `<Checkbox checked={true} label="Accept terms" onChange={fn} />`

---

## CheckboxGroup

Import: `import { CheckboxGroup } from '@/components/design-system'`
Variants: none
Sizes: none
Props:
  - label: string (optional, header label)
  - disabled: boolean (default: false, disables all children)
  - error: boolean | string (default: false)
  - children: React.ReactNode (optional, expects Checkbox children)
  - className: string (optional)
States: disabled (all children disabled), error (label turns critical, string shown below)
Gotchas: Only Checkbox children recognized; others replaced with empty fragments. Clones children to inject group state. forwardRef.
Usage: `<CheckboxGroup label="Features"><Checkbox label="A" /><Checkbox label="B" /></CheckboxGroup>`

---

## Select

Import: `import { Select } from '@/components/design-system'`
Variants: none
Sizes: none (fixed 40px height)
Props:
  - labelText: string (default: "Label")
  - placeholderText: string (default: "Placeholder text")
  - helperText: string (default: "Helper text")
  - errorText: string (default: "Error text")
  - label: boolean (default: true, controls label visibility)
  - helper: boolean (default: true, controls helper visibility)
  - disabled: boolean (default: false)
  - error: boolean (default: false)
  - state: "Default" | "Filled" | "Active" (default: "Default")
  - options: SelectOption[] (default: 4 options, { label, value, disabled? })
  - value: string (optional, controlled)
  - defaultValue: string (optional, uncontrolled)
  - onChange: (value: string) => void (optional)
  - name: string (optional)
  - required: boolean (default: false)
  - className: string (optional)
States: disabled (bg #f7f7f7, text #757575), error (border critical, label critical), active (border #000, chevron flips), hover on options (bg inverse-hover)
Gotchas: Dropdown via createPortal at z-1100. Closes on outside click AND scroll. Many props have non-empty defaults -- pass empty string to hide. forwardRef.
Usage: `<Select labelText="Country" options={[{label:"US",value:"us"}]} onChange={fn} />`

---

## Combobox

Import: `import { Combobox } from '@/components/design-system'`
Variants: none
Sizes: none (fixed 40px height)
Props:
  - label: string (optional)
  - placeholder: string (default: "Search or select...")
  - options: ComboboxOption[] (default: [], { label, value, disabled?, hiddenTag? })
  - value: string (optional, controlled single-select)
  - defaultValue: string (default: "", uncontrolled single-select)
  - values: string[] (optional, controlled multiselect)
  - defaultValues: string[] (default: [], uncontrolled multiselect)
  - onChange: (value: string | string[]) => void (optional)
  - multiselect: boolean (default: false)
  - openAnimation: "none" | "fade" | "slide" (default: "fade")
  - openDuration: number (default: 150ms)
  - closeDuration: number (default: 100ms)
  - disabled: boolean (default: false)
  - helper: string (optional)
  - error: boolean | string (optional)
  - optional: boolean (default: false, shows "(optional)" tag)
  - addNewLabel: string (optional, shows "+ Add" action)
  - onAddNew: (enteredText: string) => void (optional)
  - className: string (optional)
States: open (border active, chevron up, dropdown with animation), disabled (bg/border/text disabled), error (border critical), filtering (case-insensitive match on label or value)
Gotchas: Multiselect shows Tags when closed, search input when open. hiddenTag hides checkmark in multiselect. Dropdown via createPortal at z-1100. Close animation plays before state update. forwardRef (ref to input).
Usage: `<Combobox label="Country" options={opts} multiselect onChange={fn} />`

---

## Modal

Import: `import { Modal } from '@/components/design-system'`
Variants: none
Sizes: none (max-w-480px, min-w-320px)
Props:
  - open: boolean (required)
  - onClose: () => void (required)
  - children: React.ReactNode (required)
  - title: string (optional)
  - closeOnOverlayClick: boolean (default: true)
  - closeOnEscape: boolean (default: true)
  - className: string (optional)
  - footer: React.ReactNode (optional, sticky at bottom)
States: open (overlay rgba(51,51,51,0.5), body scroll locked, slide-in), closed (null, scroll restored)
Gotchas: Portal to document.body at z-1000. Footer makes content scrollable (flex layout). Locks body overflow. Close button always present (absolute top-right). Does NOT accept style prop.
Usage: `<Modal open={isOpen} onClose={close} title="Confirm" footer={<Button>Save</Button>}>Content</Modal>`

---

## BrandPortalModal

Import: `import { BrandPortalModal } from '@/components/design-system'`
Variants: none
Sizes: none (default 520px width, configurable)
Props:
  - open: boolean (required)
  - onClose: () => void (required)
  - title: React.ReactNode (required)
  - children: React.ReactNode (required)
  - footer: React.ReactNode (optional)
  - contextualInfo: React.ReactNode (optional, shown above footer)
  - closeOnOverlayClick: boolean (default: true)
  - closeOnEscape: boolean (default: true)
  - className: string (optional)
  - width: string (default: "520px")
States: open (overlay, scroll locked, 250ms slide-in), footer border (dynamic, shows when scrollable and not at bottom)
Gotchas: Footer border is dynamic via ResizeObserver. Desktop max-height 80dvh, mobile 90dvh. 32px horizontal padding. Title is ReactNode not just string. Portal.
Usage: `<BrandPortalModal open={isOpen} onClose={close} title="Edit" footer={<Button>Save</Button>}>Content</BrandPortalModal>`

---

## Toggle

Import: `import { Toggle } from '@/components/design-system'`
Variants: none
Sizes: none (40x24px track, 16px knob)
Props:
  - checked: boolean (default: false)
  - defaultChecked: boolean (optional, uncontrolled)
  - disabled: boolean (default: false)
  - onChange: (event: React.ChangeEvent<HTMLInputElement>) => void (optional)
  - accessibilityLabel: string (default: "Label")
  - name: string (optional)
  - className: string (optional)
States: checked (track bg #000, knob right), unchecked (track bg #dfe0e1, knob left), disabled (track bg #f7f7f7, cursor-not-allowed), focus-visible (ring)
Gotchas: role="switch". Knob uses left property with 100ms transition. No visible label prop -- use accessibilityLabel for SR, wrap externally for visible. forwardRef.
Usage: `<Toggle checked={isOn} onChange={fn} accessibilityLabel="Dark mode" />`

---

## Radio

Import: `import { Radio } from '@/components/design-system'`
Variants: none
Sizes: none (16x16px circle, 8px dot)
Props:
  - label: string (optional)
  - checked: boolean (default: false)
  - disabled: boolean (default: false)
  - onChange: (event: React.ChangeEvent<HTMLInputElement>) => void (optional)
  - name: string (optional)
  - value: string (optional)
  - className: string (optional)
States: checked (border #000, dot #000), unchecked (border #333), disabled (border #dfe0e1, cursor-not-allowed), focus-visible (ring)
Gotchas: No defaultChecked -- fully controlled only. forwardRef.
Usage: `<Radio label="Option A" checked={selected === "a"} onChange={fn} name="group1" value="a" />`

---

## RadioGroup

Import: `import { RadioGroup } from '@/components/design-system'`
Variants: none
Sizes: none
Props:
  - label: string (optional, header label)
  - disabled: boolean (default: false)
  - error: boolean | string (optional)
  - options: RadioGroupOption[] (required, { value, label })
  - checked: string (optional, controlled selected value)
  - onChecked: (value: string) => void (optional)
  - name: string (optional)
  - className: string (optional)
States: disabled (all radios disabled), error (label critical, error message shown)
Gotchas: Handler is `onChecked` not `onChange`. Options as data array, not children. forwardRef.
Usage: `<RadioGroup label="Size" options={[{value:"s",label:"Small"},{value:"m",label:"Medium"}]} checked={size} onChecked={setSize} />`

---

## SelectionCard

Import: `import { SelectionCard } from '@/components/design-system'`
Variants: none
Sizes: none
Props:
  - checked: boolean (default: false)
  - disabled: boolean (default: false)
  - label: string (required)
  - description: string (optional)
  - endSlot: React.ReactNode (optional, right-aligned content)
  - children: React.ReactNode (optional)
  - onClick: () => void (optional)
  - className: string (optional)
  - name: string (optional)
  - value: string (optional)
States: checked (border #000), unchecked (border #dfe0e1), hover (border #000), disabled (border #dfe0e1, text #757575, cursor-not-allowed), focus (keyboard via tabIndex)
Gotchas: Contains hidden radio input for form compatibility. Keyboard accessible (Enter/Space). forwardRef.
Usage: `<SelectionCard label="Standard" description="5-7 days" endSlot="$4.99" checked={method === "standard"} onClick={() => setMethod("standard")} />`

---

## TextArea

Import: `import { TextArea } from '@/components/design-system'`
Variants: none
Sizes: none (configurable rows, min 2)
Props:
  - labelText: string (default: "Label")
  - errorText: string (default: "Error text")
  - characterLimit: boolean (default: true)
  - valueText: string (optional, controlled)
  - defaultValueText: string (optional, uncontrolled)
  - placeholderText: string (default: "Placeholder text")
  - placeholder: boolean (default: true)
  - helperText: string (default: "Helper text")
  - helper: boolean (default: true)
  - resizeWebOnly: boolean (default: true, enables resize-y)
  - disabled: boolean (default: false)
  - error: boolean (default: false)
  - state: "Default" | "Active" | "Filled" (default: "Default")
  - onChange: (event: React.ChangeEvent<HTMLTextAreaElement>) => void (optional)
  - name: string (optional)
  - rows: number (default: 3, min clamped to 2)
  - maxLength: number (optional)
  - className: string (optional)
States: disabled (bg/border/text disabled), error (border/label/text critical), active (border #000), default (border subdued)
Gotchas: Character limit auto-calculates as "count/500" if no maxLength. Many boolean "show" props (placeholder, helper, characterLimit). Label always rendered. forwardRef with useImperativeHandle.
Usage: `<TextArea labelText="Description" maxLength={500} rows={5} onChange={fn} />`

---

## Badge

Import: `import { Badge } from '@/components/design-system'`
Variants: none
Sizes: none (22px height, min-width 22px)
Props:
  - value: number (default: 10)
  - showExact: boolean (default: true, false shows "99+" for values > 99)
  - className: string (optional)
States: none (static display)
Gotchas: Numeric only. Overflow "99+" only when showExact=false AND value > 99. role="status". forwardRef.
Usage: `<Badge value={42} />`

---

## Tag

Import: `import { Tag } from '@/components/design-system'`
Variants: neutral, warning, success, critical
Sizes: none (24px height)
Props:
  - label: string (default: "Label")
  - dismissible: boolean (default: false)
  - variant: "neutral" | "warning" | "success" | "critical" (default: "neutral")
  - onDismiss: () => void (optional)
  - onClick: () => void (optional)
  - className: string (optional)
States: hover (bg grey-400, neutral only), active (bg grey-400, neutral only), focused (ring-2), dismissible (shows X button, neutral only)
Gotchas: Dismiss button only for neutral variant even if dismissible=true. Hover/active only on neutral. role="button" only when interactive. Default export.
Usage: `<Tag label="Active" variant="success" />`

---

## LoadingSpinner

Import: `import { LoadingSpinner } from '@/components/design-system'`
Variants: none
Sizes: xSmall (12px), small (16px), medium (24px), large (32px), xLarge (48px)
Props:
  - size: "xSmall" | "small" | "medium" | "large" | "xLarge" (default: "small")
  - aria-label: string (default: "Loading")
  - className: string (optional)
States: none (always animating, 0.77s rotation)
Gotchas: SVG-based. Stroke width varies by size (xSmall=4.5 to xLarge=1.5). Uses currentColor from parent. NOT forwardRef.
Usage: `<LoadingSpinner size="medium" />`

---

## Link

Import: `import { Link } from '@/components/design-system'`
Variants: none
Sizes: none
Props:
  - href: string (required)
  - children: React.ReactNode (required)
  - external: boolean (default: false, opens new tab with noopener noreferrer)
  - className: string (optional)
  - onClick: (event: React.MouseEvent) => void (optional)
States: hover (opacity 80%), focus-visible (ring-2)
Gotchas: Does NOT accept style prop. Uses text-inherit + underline (inherits parent color). NOT forwardRef.
Usage: `<Link href="/about" external>Learn more</Link>`

---

## Avatar

Import: `import { Avatar } from '@/components/design-system'`
Variants: none
Sizes: xxSmall (32px), xSmall (40px), small (48px), medium (56px), large (64px), xLarge (96px)
Props:
  - label: string (default: "F", first character used as initial)
  - size: "xxSmall" | "xSmall" | "small" | "medium" | "large" | "xLarge" (default: "xxSmall")
  - image: { url: string; alt_text: string } (optional)
  - iconProps: {} (optional, truthy enables icon indicator dot)
  - borderColor: string (optional, adds ring-2 border)
  - className: string (optional)
States: image loaded (shows image), image error (falls back to initials), no image (initial on #333 bg)
Gotchas: iconProps is typed as {} -- any truthy value triggers indicator dot. Uses clsx (unique to this component). borderColor via CSS custom property trick. forwardRef.
Usage: `<Avatar size="medium" image={{url: "pic.jpg", alt_text: "User"}} />`

---

## LoadingSkeleton

Import: `import { LoadingSkeleton } from '@/components/design-system'`
Variants: canvas, square, circle, titleText, paragraphText
Sizes: none (determined by variant + props)
Props:
  - variant: "canvas" | "square" | "circle" | "titleText" | "paragraphText" (default: "canvas")
  - width: number | string (optional, for canvas/titleText/paragraphText)
  - height: number | string (optional, for canvas)
  - size: number | string (default: 240, for square)
  - diameter: number | string (default: 64, for circle)
  - className: string (optional)
States: none (shimmer animation, 1.8s ease-in-out infinite)
Gotchas: canvas=width/height, square=size for both dims, circle=diameter with border-radius 9999px, titleText=32px height, paragraphText=20px height. NOT forwardRef.
Usage: `<LoadingSkeleton variant="paragraphText" width={200} />`

---

## ProgressBar

Import: `import { ProgressBar } from '@/components/design-system'`
Variants: success, neutral
Sizes: none (8px height track)
Props:
  - progress: number (default: 0, range 0-1, clamped)
  - variant: "success" | "neutral" (default: "success")
  - showIcon: boolean (default: true, checkmark at 100% for success)
  - accessibilityLabel: string (default: "Label")
  - className: string (optional)
States: 0% (empty grey-400 track), partial (filled), 100% success (full + optional checkmark), 100% neutral (full, no icon)
Gotchas: Progress is 0-1 NOT 0-100. Non-finite values default to 0. Success uses green-700, neutral uses black. forwardRef.
Usage: `<ProgressBar progress={0.75} variant="success" />`

---

## IconButton

Import: `import { IconButton } from '@/components/design-system'`
Variants: primary, secondary, tertiary
Sizes: xxSmall (24px), xSmall (32px), small (40px), medium (48px)
Props:
  - Icon: React.FC<any> (required, icon component reference)
  - variant: "primary" | "secondary" | "tertiary" (default: "primary")
  - shape: "circle" | "square" (default: "circle")
  - size: "xxSmall" | "xSmall" | "small" | "medium" (default: "medium")
  - disabled: boolean (default: false)
  - loading: boolean (default: false)
  - onClick: (event: React.MouseEvent) => void (optional)
  - aria-label: string (optional)
  - className: string (optional)
States: hover (bg darkens), active (same as hover), disabled (bg #f7f7f7, icon #757575), loading (spinner, hides icon), focus-visible (ring-2)
Gotchas: Icon prop is a component (React.FC), NOT a ReactNode -- pass component itself, not `<Icon />`. Icon sizes scale with button (xxSmall=12, xSmall=16, small=20, medium=24). forwardRef.
Usage: `<IconButton Icon={CloseIcon} variant="tertiary" size="small" aria-label="Close" onClick={fn} />`

---

## PasswordInput

Import: `import { PasswordInput } from '@/components/design-system'`
Variants: none
Sizes: none (40px height)
Props:
  - label: string (optional)
  - placeholder: string (optional)
  - value: string (optional, controlled)
  - defaultValue: string (optional, uncontrolled)
  - onChange: (event: React.ChangeEvent<HTMLInputElement>) => void (optional)
  - disabled: boolean (default: false)
  - error: boolean | string (optional)
  - helper: string (optional)
  - name: string (optional)
  - defaultShowPassword: boolean (default: false)
  - className: string (optional)
States: disabled (bg #f7f7f7, toggle disabled), error (border #d17e70, label #921100), show/hide password (toggles type), focus (outline #000)
Gotchas: Built-in show/hide toggle (eye icons). autoComplete="off" and spellCheck=false hardcoded. Uses hardcoded hex. forwardRef.
Usage: `<PasswordInput label="Password" placeholder="Enter password" onChange={fn} />`

---

## Search

Import: `import { Search } from '@/components/design-system'`
Variants: none
Sizes: none (40px height, rounded-full)
Props:
  - placeholder: string (default: "Search...")
  - value: string (optional, controlled)
  - defaultValue: string (optional, uncontrolled)
  - onChange: (event: React.ChangeEvent<HTMLInputElement>) => void (optional)
  - onClear: () => void (optional, enables clear button)
  - disabled: boolean (default: false)
  - name: string (optional)
  - className: string (optional)
States: disabled (bg/border/text disabled), has value + onClear (shows X button), focus (outline active)
Gotchas: Clear button only when onClear provided AND has value AND not disabled. onClear does NOT auto-clear -- parent must handle. Pill shape (rounded-full). Search icon always on left. forwardRef.
Usage: `<Search value={query} onChange={fn} onClear={() => setQuery("")} placeholder="Search products" />`

---

## AccordionGroup

Import: `import { AccordionGroup } from '@/components/design-system'`
Variants: none
Sizes: none
Props:
  - children: React.ReactNode (required)
  - className: string (optional)
States: none (container only, border-top wrapper)
Gotchas: Simple wrapper div. Named export from Accordion.tsx.
Usage: `<AccordionGroup><AccordionItem label="Section 1" /><AccordionItem label="Section 2" /></AccordionGroup>`

---

## AccordionItem

Import: `import { AccordionItem } from '@/components/design-system'`
Variants: none
Sizes: none
Props:
  - label: string (required)
  - descriptionText: string (default: "Description")
  - contentSlot: React.ReactNode | null (default: null)
  - expanded: boolean (optional, controlled)
  - contentType: "None" | "Description" | "Custom Slot" (default: "None")
  - onToggle: (expanded: boolean) => void (optional)
  - className: string (optional)
States: expanded (chevron up, content visible with max-h transition), collapsed (chevron down, hidden), hover (text hover, bg inverse-hover), focus (ring)
Gotchas: Content always in DOM (hidden with max-height for smooth animation). contentType must be "Description" or "Custom Slot" to render content. "Custom Slot" without contentSlot shows purple placeholder. Supports controlled + uncontrolled. forwardRef (ref to button).
Usage: `<AccordionItem label="FAQ" contentType="Description" descriptionText="Answer here" />`

---

## Tooltip

Import: `import { Tooltip } from '@/components/design-system'`
Variants: none
Sizes: none
Props:
  - children: React.ReactNode (required, trigger element)
  - label: string (required, tooltip text)
  - position: "top" | "bottom" | "left" | "right" (default: "top")
  - alignment: "start" | "middle" | "end" (default: "middle")
  - className: string (optional)
States: hover/focus on trigger (fadeIn 0.15s), mouseLeave/blur (disappears)
Gotchas: Portal to document.body at z-50. pointer-events-none. Position calculated after render. Trigger wrapped in div (may affect inline layouts). NOT forwardRef.
Usage: `<Tooltip label="More info" position="bottom"><Button>Hover me</Button></Tooltip>`

---

## Popover

Import: `import { Popover } from '@/components/design-system'`
Variants: none
Sizes: none (280px width)
Props:
  - open: boolean (default: true)
  - position: "top" | "bottom" | "left" | "right" (default: "top")
  - alignment: "start" | "middle" | "end" (default: "start")
  - title: string (default: "Title")
  - description: string (default: example string)
  - footerVariant: "action" | "customSlot" (default: "action")
  - actionButtonProps: { label: string; onClick?: () => void } (optional)
  - onClose: () => void (optional, shows close button)
  - triggerRef: React.RefObject<HTMLElement | null> (optional, for positioning)
  - className: string (optional)
States: open (positioned relative to triggerRef, dark bg #333, inverse text), closed (null)
Gotchas: Without triggerRef, falls back to center-of-viewport. Viewport edge clamping with 16px padding. Portal at z-50. Dark background with inverse text. Close button only when onClose provided. forwardRef.
Usage: `<Popover open={isOpen} triggerRef={buttonRef} title="Help" description="Click here to..." onClose={close} actionButtonProps={{label:"Got it"}} />`

---

## Toast

Import: `import { Toast } from '@/components/design-system'`
Variants: none
Sizes: none
Props:
  - description: string (required)
  - open: boolean (default: false)
  - cta: string (optional, CTA button text, replaces close button)
  - onCtaClick: () => void (optional)
  - onDismiss: () => void (optional)
  - timeout: number | false (default: 4000ms, false disables auto-dismiss)
  - className: string (optional)
States: open (slides up from bottom-center), closing (slides down, 200ms), auto-dismiss (after timeout)
Gotchas: Fixed bottom-center at z-9999. CTA replaces close button (not both). CTA click also calls onDismiss. Auto-dismiss is 6000ms when cta set, otherwise timeout prop. Always shows info icon. NOT forwardRef.
Usage: `<Toast description="Item saved" open={show} onDismiss={() => setShow(false)} />`

---

## Tab

Import: `import { Tab } from '@/components/design-system'`
Variants: none
Sizes: none
Props:
  - label: string (required)
  - value: string (required)
  - count: number (optional, shows count badge)
  - selected: boolean (default: false)
  - onSelect: (value: string) => void (optional)
  - disabled: boolean (default: false)
  - className: string (optional)
States: selected (font-medium, text-primary, 2px bottom border), unselected (font-normal, text-subdued, hover: text-primary), disabled (opacity 50%, cursor-not-allowed), focus-visible (ring-2)
Gotchas: When used with TabGroup, selected and onSelect are auto-injected via cloneElement.
Usage: `<Tab label="All" value="all" />`

---

## TabGroup

Import: `import { TabGroup } from '@/components/design-system'`
Variants: none
Sizes: none
Props:
  - selectedTabValue: string (optional)
  - onSelect: (value: string) => void (optional)
  - children: React.ReactNode (required, Tab children)
  - className: string (optional)
  - hideBorder: boolean (default: false)
States: keyboard navigation (ArrowLeft/Right/Up/Down cycles, Home/End jump)
Gotchas: Clones Tab children to inject selected/onSelect. gap-10 (40px) between tabs. NOT forwardRef.
Usage: `<TabGroup selectedTabValue={tab} onSelect={setTab}><Tab label="All" value="all" /><Tab label="Active" value="active" /></TabGroup>`

---

## TabPanel

Import: `import { TabPanel } from '@/components/design-system'`
Variants: none
Sizes: none
Props:
  - tabValue: string (required)
  - selectedTabValue: string (optional)
  - children: React.ReactNode (required)
  - className: string (optional)
States: none (renders or returns null)
Gotchas: Returns null when tabValue !== selectedTabValue. NOT forwardRef.
Usage: `<TabPanel tabValue="all" selectedTabValue={tab}>Content</TabPanel>`

---

## Pagination

Import: `import { Pagination } from '@/components/design-system'`
Variants: none
Sizes: none
Props:
  - currentPage: number (required, 1-indexed)
  - totalPages: number (required)
  - pageAmount: number (default: 5, snaps to 3/5/7)
  - onClick: (page: number) => void (optional)
  - className: string (optional)
States: current (dark bg, inverse text, font-medium), other (hover bg inverse-hover), prev/next disabled (at first/last page)
Gotchas: Returns null when totalPages <= 1. pageAmount snapped to 3, 5, or 7. 32x32px page buttons. forwardRef.
Usage: `<Pagination currentPage={page} totalPages={50} onClick={setPage} />`

---

## Menu

Import: `import { Menu, MenuTrigger, MenuItem } from '@/components/design-system'`
Variants: none
Sizes: none
Props (Menu):
  - alignment: "start" | "end" (default: "end")
  - children: React.ReactNode (optional, MenuTrigger + MenuItem children)
  - aria-label: string (default: "Label")
  - className: string (optional)
Props (MenuTrigger):
  - children: React.ReactNode (required, receives onClick/aria props)
Props (MenuItem):
  - label: string (default: "Label")
  - Icon: React.FC<any> (optional)
  - onClick: () => void (optional)
States: open (dropdown visible), closed (hidden), item hover (bg inverse-hover), closes on (outside click, Escape, item click)
Gotchas: MenuTrigger clones child to inject onClick/aria. Dropdown is absolutely positioned (not portal), z-50. Uses React Context. Menu is default export; MenuTrigger/MenuItem are named.
Usage: `<Menu><MenuTrigger><Button>Actions</Button></MenuTrigger><MenuItem label="Edit" onClick={fn} /><MenuItem label="Delete" onClick={fn} /></Menu>`

---

## FilterButton

Import: `import { FilterButton } from '@/components/design-system'`
Variants: rest, applied
Sizes: none (40px height, pill shape)
Props:
  - label: string (required)
  - Icon: React.FC<any> (optional)
  - variant: "rest" | "applied" (default: "rest")
  - disabled: boolean (default: false)
  - onClick: (event: React.MouseEvent) => void (optional)
  - type: "button" | "submit" | "reset" (default: "button")
  - className: string (optional)
States: rest (white bg, subdued border, hover: border darkens), applied (dark bg #333, inverse text, hover: bg #000), disabled (bg/border/text disabled), focus-visible (ring-2)
Gotchas: Pill shape (rounded-full). forwardRef.
Usage: `<FilterButton label="Category" variant="applied" onClick={fn} />`

---

## FilterToggle

Import: `import { FilterToggle } from '@/components/design-system'`
Variants: none (uses applied boolean)
Sizes: none (40px height, pill shape)
Props:
  - label: string (default: "Label")
  - Icon: React.FC<any> (optional)
  - applied: boolean (default: false)
  - disabled: boolean (default: false)
  - onClick: (event: React.MouseEvent) => void (optional)
  - customBackgroundColor: string (optional)
  - defaultContentColor: string (optional, overrides text + icon color)
  - customBorderColor: string (optional)
  - className: string (optional)
States: rest (white bg, subdued border, hover: darkens), applied (dark bg, no border, inverse text, hover: darkens), disabled (bg disabled, text disabled), focus-visible (ring-2)
Gotchas: aria-pressed from applied. Custom color props override token-based styles with inline styles. Prop is defaultContentColor not customTextColor. Pill shape. forwardRef.
Usage: `<FilterToggle label="New" applied={isNew} onClick={toggle} />`

---

## SizeToggle

Import: `import { SizeToggle } from '@/components/design-system'`
Variants: none
Sizes: none (40px height, min-width 40px)
Props:
  - label: string (default: "Label")
  - active: boolean (default: false)
  - disabled: boolean (default: false)
  - onClick: (event: React.MouseEvent) => void (optional)
  - accessibilityLabel: string (optional, defaults to label)
  - className: string (optional)
States: active (border #333, font-medium), inactive (border #dfe0e1, font-regular), hover (border darkens), disabled (opacity 50%, cursor-not-allowed)
Gotchas: Square shape with 4px radius (not pill). Uses inline styles instead of Tailwind. aria-pressed from active. forwardRef.
Usage: `<SizeToggle label="M" active={size === "M"} onClick={() => setSize("M")} />`

---

## FilterGroup

Import: `import { FilterGroup } from '@/components/design-system'`
Variants: none
Sizes: none
Props:
  - aria-label: string (default: "Label")
  - items: FilterGroupItem[] (required, { label, applied?, Icon?, aria-label?, onClick? })
  - filterButtonProps: { label, Icon?, onClick?, aria-label? } (optional, adds a FilterButton)
  - className: string (optional)
States: none (delegates to FilterToggle/FilterButton children)
Gotchas: FilterButton always renders variant="rest". Uses index as key. Flex-wrap with gap from dimensions-2. forwardRef.
Usage: `<FilterGroup items={[{label:"New",applied:true},{label:"Sale"}]} filterButtonProps={{label:"All filters"}} />`

---

## Design Tokens

### Colors — Primitives

| CSS Variable | Value | Tailwind | Usage |
|---|---|---|---|
| `--slate-color-grey-100` | #ffffff | `bg-grey-100` | White |
| `--slate-color-grey-200` | #fbfbfb | `bg-grey-200` | Near-white |
| `--slate-color-grey-300` | #f7f7f7 | `bg-grey-300` | Light grey / disabled bg |
| `--slate-color-grey-400` | #dfe0e1 | `border-grey-400` | Border grey / disabled border |
| `--slate-color-grey-700` | #757575 | `text-grey-700` | Subdued / muted text |
| `--slate-color-grey-900` | #333333 | `text-grey-900` | Primary text |
| `--slate-color-grey-1000` | #000000 | `text-grey-1000` | True black / hover states |
| `--slate-color-neutral-100` | #fbf8f6 | `bg-neutral-100` | Warm off-white |
| `--slate-color-neutral-600` | #b5a998 | `text-neutral-600` | Warm mid-tone |
| `--slate-color-neutral-1000` | #585550 | `text-neutral-1000` | Warm dark |
| `--slate-color-red-100` | #f2e5e1 | `bg-red-100` | Light red surface |
| `--slate-color-red-200` | #e9e0dc | `bg-red-200` | Muted red surface |
| `--slate-color-red-400` | #ba9b88 | `text-red-400` | Muted warm mid |
| `--slate-color-red-500` | #d17e70 | `border-red-500` | Salmon / critical border |
| `--slate-color-red-700` | #921100 | `text-red-700` | Deep red / critical text |
| `--slate-color-red-800` | #5a1e09 | `text-red-800` | Dark red |
| `--slate-color-red-1000` | #390d18 | `text-red-1000` | Darkest red |
| `--slate-color-blue-100` | #f2f5f5 | `bg-blue-100` | Light blue surface |
| `--slate-color-blue-200` | #e2e7f0 | `bg-blue-200` | Light blue / info surface |
| `--slate-color-blue-400` | #7a7885 | `text-blue-400` | Muted blue-grey |
| `--slate-color-blue-500` | #667495 | `text-blue-500` | Mid blue |
| `--slate-color-blue-700` | #275ec5 | `text-blue-700` | Focus ring / accent blue |
| `--slate-color-blue-1000` | #1b2834 | `text-blue-1000` | Dark navy |
| `--slate-color-teal-600` | #36676a | `text-teal-600` | Teal mid |
| `--slate-color-teal-900` | #154548 | `text-teal-900` | Dark teal |
| `--slate-color-orange-100` | #f6eee4 | `bg-orange-100` | Light orange surface |
| `--slate-color-orange-400` | #ab7456 | `text-orange-400` | Warm mid-tone |
| `--slate-color-orange-700` | #e65126 | `text-orange-700` | Bright orange |
| `--slate-color-orange-1000` | #7d3e1e | `text-orange-1000` | Dark orange / sale text |
| `--slate-color-yellow-100` | #f6efdb | `bg-yellow-100` | Light yellow surface |
| `--slate-color-yellow-200` | #ece3d2 | `bg-yellow-200` | Warm cream |
| `--slate-color-yellow-400` | #d1b985 | `border-yellow-400` | Gold mid |
| `--slate-color-yellow-600` | #a79764 | `text-yellow-600` | Dark gold |
| `--slate-color-yellow-800` | #907c3a | `text-yellow-800` | Olive gold |
| `--slate-color-yellow-1000` | #595540 | `text-yellow-1000` | Dark olive |
| `--slate-color-green-100` | #e9f1e5 | `bg-green-100` | Light green surface |
| `--slate-color-green-200` | #dde3d0 | `bg-green-200` | Sage light |
| `--slate-color-green-300` | #d9d8cd | `bg-green-300` | Muted sage |
| `--slate-color-green-500` | #91957b | `text-green-500` | Olive mid |
| `--slate-color-green-600` | #91a793 | `border-green-600` | Sage green |
| `--slate-color-green-700` | #49694c | `text-green-700` | Forest green / success |
| `--slate-color-green-1000` | #3e4023 | `text-green-1000` | Dark forest |
| `--slate-color-transparent-grey-0` | rgba(51,51,51,0) | `bg-transparentgrey-0` | Fully transparent |
| `--slate-color-transparent-grey-100` | rgba(51,51,51,0.05) | `bg-transparentgrey-100` | 5% overlay |
| `--slate-color-transparent-grey-200` | rgba(51,51,51,0.1) | `bg-transparentgrey-200` | 10% overlay |
| `--slate-color-transparent-grey-500` | rgba(51,51,51,0.5) | `bg-transparentgrey-500` | 50% mask |

### Colors — Semantic: Action

| CSS Variable | Value | Usage |
|---|---|---|
| `--slate-color-action-surface-default` | #333333 (grey-900) | Primary button fill |
| `--slate-color-action-surface-hover` | #000000 (grey-1000) | Button fill hover |
| `--slate-color-action-surface-active` | #000000 (grey-1000) | Button fill active |
| `--slate-color-action-surface-disabled` | #f7f7f7 (grey-300) | Button fill disabled |
| `--slate-color-action-surface-inverse` | #ffffff (grey-100) | Inverse button fill |
| `--slate-color-action-surface-inverse-hover` | #f7f7f7 (grey-300) | Inverse hover |
| `--slate-color-action-surface-inverse-active` | #f7f7f7 (grey-300) | Inverse active |
| `--slate-color-action-surface-favorited` | #921100 | Favorited heart |
| `--slate-color-action-surface-critical` | #f2e5e1 | Critical surface |
| `--slate-color-action-surface-critical-hover` | #921100 | Critical hover |
| `--slate-color-action-text-default` | #333333 (grey-900) | Button text default |
| `--slate-color-action-text-inverse` | #ffffff (grey-100) | Inverse button text |
| `--slate-color-action-text-hover` | #000000 (grey-1000) | Button text hover |
| `--slate-color-action-text-disabled` | #757575 (grey-700) | Button text disabled |
| `--slate-color-action-text-placeholder` | #757575 (grey-700) | Input placeholder |
| `--slate-color-action-text-critical` | #921100 (red-700) | Critical text |
| `--slate-color-action-icon-default` | #333333 (grey-900) | Icon default |
| `--slate-color-action-icon-inverse` | #ffffff (grey-100) | Inverse icon |
| `--slate-color-action-icon-disabled` | #757575 (grey-700) | Icon disabled |
| `--slate-color-action-border-default` | #333333 (grey-900) | Button border |
| `--slate-color-action-border-subdued` | #dfe0e1 (grey-400) | Subtle border |
| `--slate-color-action-border-inverse` | #ffffff (grey-100) | Inverse border |
| `--slate-color-action-border-hover` | #000000 (grey-1000) | Border hover |
| `--slate-color-action-border-disabled` | #dfe0e1 (grey-400) | Border disabled |
| `--slate-color-action-border-focus-ring` | #275ec5 (blue-700) | Focus ring |
| `--slate-color-action-border-critical` | #d17e70 (red-500) | Critical border |

### Colors — Semantic: Message

| CSS Variable | Value | Usage |
|---|---|---|
| `--slate-color-message-text-critical` | #921100 (red-700) | Error text |
| `--slate-color-message-text-sale` | #7d3e1e (orange-1000) | Sale price |
| `--slate-color-message-icon-success` | #49694c (green-700) | Success icon |
| `--slate-color-message-icon-warning` | #907c3a (yellow-800) | Warning icon |
| `--slate-color-message-icon-critical` | #921100 (red-700) | Error icon |
| `--slate-color-message-icon-info` | #1b2834 (blue-1000) | Info icon |
| `--slate-color-message-border-success` | #91a793 (green-600) | Success border |
| `--slate-color-message-border-warning` | #d1b985 (yellow-400) | Warning border |
| `--slate-color-message-border-critical` | #d17e70 (red-500) | Error border |
| `--slate-color-message-border-info` | #7a7885 (blue-400) | Info border |
| `--slate-color-message-surface-success` | #e9f1e5 (green-100) | Success bg |
| `--slate-color-message-surface-warning` | #f6efdb (yellow-100) | Warning bg |
| `--slate-color-message-surface-critical` | #f2e5e1 (red-100) | Error bg |
| `--slate-color-message-surface-info` | #e2e7f0 (blue-200) | Info bg |

### Colors — Semantic: Program

| CSS Variable | Value | Usage |
|---|---|---|
| `--slate-color-program-surface-top-shop-primary` | #dde3d0 (green-200) | Top Shop badge bg |
| `--slate-color-program-surface-top-shop-primary-inverse` | #3e4023 (green-1000) | Top Shop dark bg |
| `--slate-color-program-surface-insider-primary` | #f2f5f5 (blue-100) | Insider badge bg |
| `--slate-color-program-surface-insider-primary-inverse` | #154548 (teal-900) | Insider dark bg |
| `--slate-color-program-surface-faire-source` | #f6f2e8 | Faire Source bg |
| `--slate-color-program-surface-market-primary` | #56160c | Market dark bg |
| `--slate-color-program-surface-market-primary-inverse` | #f8f1e9 | Market light bg |
| `--slate-color-program-surface-faire-direct-primary` | #e8eeff | Faire Direct bg |
| `--slate-color-program-icon-top-shop` | #91957b (green-500) | Top Shop icon |
| `--slate-color-program-icon-insider` | #36676a (teal-600) | Insider icon |
| `--slate-color-program-icon-faire-source` | #735b21 | Faire Source icon |
| `--slate-color-program-text-top-shop` | #3e4023 (green-1000) | Top Shop text |
| `--slate-color-program-text-insider` | #154548 (teal-900) | Insider text |

### Colors — Semantic: Expressive

| CSS Variable | Value | Usage |
|---|---|---|
| `--slate-color-expressive-surface-neutral` | #fbf8f6 (neutral-100) | Neutral bg |
| `--slate-color-expressive-surface-neutral-inverse` | #585550 (neutral-1000) | Neutral dark bg |
| `--slate-color-expressive-surface-yellow` | #ece3d2 (yellow-200) | Yellow bg |
| `--slate-color-expressive-surface-yellow-inverse` | #595540 (yellow-1000) | Yellow dark bg |
| `--slate-color-expressive-surface-green` | #d9d8cd (green-300) | Green bg |
| `--slate-color-expressive-surface-green-inverse` | #3e4023 (green-1000) | Green dark bg |
| `--slate-color-expressive-surface-blue` | #e2e7f0 (blue-200) | Blue bg |
| `--slate-color-expressive-surface-blue-inverse` | #1b2834 (blue-1000) | Blue dark bg |
| `--slate-color-expressive-surface-red` | #e9e0dc (red-200) | Red bg |
| `--slate-color-expressive-surface-red-inverse` | #390d18 (red-1000) | Red dark bg |
| `--slate-color-expressive-surface-orange` | #f6eee4 (orange-100) | Orange bg |
| `--slate-color-expressive-surface-orange-inverse` | #7d3e1e (orange-1000) | Orange dark bg |

### Spacing

| CSS Variable | Value | Tailwind | Usage |
|---|---|---|---|
| `--spacing-0` | 0 | `gap-0` | No spacing |
| `--spacing-1` | 0.25rem (4px) | `gap-1` | Smallest |
| `--spacing-2` | 0.5rem (8px) | `gap-2` | Default element spacing |
| `--spacing-3` | 0.75rem (12px) | `gap-3` | |
| `--spacing-4` | 1rem (16px) | `gap-4` | Default section spacing |
| `--spacing-6` | 1.5rem (24px) | `gap-6` | |
| `--spacing-8` | 2rem (32px) | `gap-8` | |
| `--spacing-12` | 3rem (48px) | `gap-12` | |
| `--spacing-16` | 4rem (64px) | `gap-16` | |
| `--spacing-24` | 6rem (96px) | `gap-24` | |
| `--spacing-32` | 8rem (128px) | `gap-32` | |
| `--spacing-48` | 12rem (192px) | `gap-48` | |
| `--slate-spacing-element-default` | 8px (dimensions-2) | -- | Default element gap |
| `--slate-spacing-element-large` | 16px (dimensions-4) | -- | Large element gap |
| `--slate-spacing-group-default` | 16px (dimensions-4) | -- | Default group gap |
| `--slate-spacing-section-default` | 32px (dimensions-8) | -- | Default section gap |

### Dimensions

| CSS Variable | Value | Tailwind | Usage |
|---|---|---|---|
| `--slate-dimensions-1` | 4px | `size-1` | Base unit |
| `--slate-dimensions-2` | 8px | `size-2` | |
| `--slate-dimensions-3` | 12px | `size-3` | |
| `--slate-dimensions-4` | 16px | `size-4` | Input padding, small button padding |
| `--slate-dimensions-5` | 20px | `size-5` | Checkbox size |
| `--slate-dimensions-6` | 24px | `size-6` | Modal padding, button padding-x |
| `--slate-dimensions-7` | 28px | `size-7` | |
| `--slate-dimensions-8` | 32px | `size-8` | |
| `--slate-dimensions-9` | 36px | `size-9` | |
| `--slate-dimensions-10` | 40px | `size-10` | Small button height, input height |
| `--slate-dimensions-11` | 44px | `size-11` | |
| `--slate-dimensions-12` | 48px | `size-12` | Default button height |
| `--slate-dimensions-13` | 52px | `size-13` | |
| `--slate-dimensions-14` | 56px | `size-14` | |
| `--slate-dimensions-15` | 60px | `size-15` | |
| `--slate-dimensions-16` | 64px | `size-16` | |
| `--slate-dimensions-17` | 68px | `size-17` | |
| `--slate-dimensions-18` | 72px | `size-18` | |

### Radii

| CSS Variable | Value | Usage |
|---|---|---|
| `--slate-radius-component-default` | 4px (dimensions-1) | Buttons, inputs, cards |
| `--slate-radius-component-round` | 999px | Pills, search, filter toggles |
| `--slate-radius-component-checkbox` | 2px | Checkbox corners |
| `--slate-radius-component-tag` | 2px | Tag corners |
| `--slate-radius-container-default` | 4px (dimensions-1) | Container/card rounding |

---

## Typography Presets

| Preset | Font | Size | Weight | Line Height | Letter Spacing | Tailwind |
|---|---|---|---|---|---|---|
| Body Small / Tier 100 | Graphik | 12px | 400 | 16px | 0 | `text-100 leading-100` |
| Body / Tier 200 | Graphik | 14px | 400 | 20px | 0.15px | `text-200 leading-200` |
| Body Large / Tier 300 | Graphik | 18px | 400 | 26px | 0 | `text-300 leading-300` |
| Heading Small / Tier 400 | Graphik | 22px | 500 | 32px | 0 | `text-400 leading-400` |
| Heading / Tier 500 | Graphik | 30px | 500 | 38px | 0 | `text-500 leading-500` |
| Display Small / Tier 600 | Graphik | 38px | 500 | 50px | 0 | `text-600 leading-600` |
| Display / Tier 700 | Graphik | 52px | 500 | 64px | 0 | `text-700 leading-700` |
| Display Large / Tier 800 | Graphik | 72px | 500 | 86px | 0 | -- |

Font families:
- Sans: `"Graphik", -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif` (`font-sans`)
- Serif: `"Nantes_fix", Georgia, serif` (`font-serif`)

Font weights:
- Regular: 400 (`font-regular`)
- Medium: 500 (`font-medium`)
- Semibold: 600 (`font-semibold`)

---

## Icons

### Built-in (SlateIcons)

Available: ChevronLeftIcon, ChevronRightIcon, ChevronDownIcon, ChevronUpIcon, CloseIcon, LockIcon, CheckIcon, CheckCircleSolidIcon, SearchIcon, InfoIcon, WarningIcon, ShieldCheckIcon, FaireLogo
Import: `import { ChevronDownIcon, CloseIcon, SearchIcon, ... } from '@/components/icons/SlateIcons'`
Props: size (number, default 24), className (string), color (string, default "currentColor")
Namespace: `SlateIcons.ChevronLeft`, `SlateIcons.Close`, etc.
Note: CheckCircleSolidIcon defaults to size 16. FaireLogo has different props: width (98), height (24), color ("#333333").

### Built-in (Other)

CompassBasicIcon: `import CompassBasicIcon from '@/components/icons/CompassBasicIcon'` — Props: className, size (default 20)
PlusIcon: `import { PlusIcon } from '@/components/fauxds/FauxDSHelpers'` — 24x24 SVG, stroke currentColor

### External (@phosphor-icons/react v2.1.10)

Used in codebase: ArrowClockwise, ArrowCounterClockwise, ArrowDown, ArrowLeft, ArrowRight, ArrowSquareOut, ArrowUp, ArrowsDownUp, ArrowsLeftRight, Atom, Book, CardsThree, CaretDown, CaretLeft, CaretRight, CaretUp, ChatCircle, Check, Compass, Copy, Crosshair, DotsSixVertical, DotsThree, DotsThreeVertical, DownloadSimple, Eye, EyeSlash, FadersHorizontal, File, Fire, FloppyDisk, Flower, FolderOpen, Funnel, GridFour, Heart, Info, Leaf, Lifebuoy, Lightning, LinkSimple, List, MagnifyingGlass, Minus, Moon, Package, PaperPlaneTilt, Pause, Pencil, PencilSimple, Play, Plus, Rows, Shapes, ShoppingCart, SlidersHorizontal, SortAscending, Sparkle, SpeakerHigh, SpeakerSlash, SquaresFour, Star, Sun, Tag, Timer, Trash, TrendUp, UploadSimple, Users, Warning, WarningCircle, Waves, X
Import: `import { IconName } from "@phosphor-icons/react"`

---

## Common Patterns

### Input Field Styling

```jsx
<input
  className="h-[40px] w-full rounded-[4px] border px-[16px]
    border-[#dfe0e1] bg-white text-[#333333]
    placeholder:text-[#757575]
    focus:outline focus:outline-1 focus:-outline-offset-1 focus:outline-[#000000]
    transition-all duration-150 ease-in-out"
  style={{
    fontFamily: "var(--font-family-sans, 'Graphik', sans-serif)",
    fontSize: "14px", fontWeight: 400,
    lineHeight: "20px", letterSpacing: "0.15px",
  }}
/>
```
Notes: All text inputs use h-40px, 4px radius, 16px padding-x, #dfe0e1 border, black outline on focus, 150ms transition. Error swaps border to #d17e70. Disabled uses bg-#f7f7f7 + text-#757575. Search uses rounded-full instead of rounded-[4px].

### Label + Input + Helper Stack

```jsx
<div className="flex flex-col">
  <label className="mb-[8px] text-[#333333]"
    style={{ fontFamily: "var(--font-family-sans)", fontSize: "14px", fontWeight: 400, lineHeight: "20px", letterSpacing: "0.15px" }}>
    {label}{required && <span className="ml-1 text-[#921100]">*</span>}
  </label>
  <input ... />
  <span className="mt-[4px] text-[#757575]"
    style={{ fontSize: "12px", lineHeight: "16px" }}>
    {helperText}
  </span>
</div>
```
Notes: Used by TextInput, Select, PasswordInput, TextArea, Checkbox. Error label turns text-[#921100], error helper turns text-[#921100]. Spacing: mb-8px label-to-input, mt-4px input-to-helper.

### Portal Dropdown

```jsx
const rect = buttonRef.current.getBoundingClientRect();
setDropdownPosition({ top: rect.bottom + 4, left: rect.left, width: rect.width });

{isOpen && createPortal(
  <div
    ref={dropdownRef}
    className="fixed z-1100 rounded-[4px] border border-[#dfe0e1] bg-white shadow-lg"
    style={{ top, left, width, maxHeight, overflowY: "auto",
      boxShadow: "0 2px 12px rgba(0, 0, 0, 0.15)" }}
  >
    {options}
  </div>,
  document.body
)}
```
Notes: Select, Popover, Tooltip, Modal all use createPortal. 4px gap below trigger. Select closes on outside click + scroll. Z-index: Select=1100, Modal=1000, Tooltip=50, Toast=9999.

### Modal Composition

```jsx
<Modal open={isOpen} onClose={close} title="Title"
  footer={<div className="flex gap-3 justify-end">
    <Button variant="secondary" onClick={onClose}>Cancel</Button>
    <Button onClick={onConfirm}>Confirm</Button>
  </div>}>
  {scrollable content}
</Modal>
```
Notes: Overlay rgba(51,51,51,0.5) with grid centering. Card: max-w-480px min-w-320px max-h-90vh rounded-8px p-24px. Close button: absolute top-right 32x32 circle. Footer: mt-6 border-t pt-6. Body scroll locked. Escape + overlay click dismissal.

### Variant Style Map

```jsx
const getVariantStyles = () => {
  if (disabled) return { bg: "bg-[#f7f7f7]", border: "border-[#dfe0e1]", text: "text-[#757575]" };
  if (error)    return { bg: "bg-white", border: "border-[#d17e70]", text: "text-[#333]" };
  return { bg: "bg-white", border: "border-[#dfe0e1]", text: "text-[#333]" };
};
```
Notes: Every FauxDS component uses this pattern -- function returning Tailwind class objects keyed by role (bg, border, text, icon). Used in Button, TextInput, Select, Checkbox, IconButton, Tag, Tab, Search. Newer components use CSS variable references.

### Focus Ring

```jsx
className="focus:outline-hidden focus-visible:ring-2 focus-visible:ring-offset-2
  focus-visible:ring-[var(--slate-color-action-border-focus-ring)]"
```
Notes: All interactive elements (Button, IconButton, Select, Tab, Checkbox, Menu items). Token: --slate-color-action-border-focus-ring (#275ec5). Some use focus:outline-hidden + focus-visible:outline variant.

### Typography Inline Style

```jsx
style={{
  fontFamily: "var(--slate-typography-family-sans-serif)",
  fontSize: "var(--slate-typography-font-size-200)",     // 14px
  fontWeight: "var(--slate-typography-weight-regular)",   // 400
  lineHeight: "var(--slate-typography-line-height-200)",  // 20px
  letterSpacing: "var(--slate-typography-letter-spacing-200)", // 0.15px
}}
```
Notes: Applied via inline style (not className) on labels, inputs, helper text, options. Tier 100 (12px/16px) for small text. Tier 200 (14px/20px) for standard body/input. Modal titles use hardcoded 22px/500.

### className Whitespace Normalization

```jsx
className={`some-class ${conditionalClass} ${anotherClass}`
  .replace(/\s+/g, " ")
  .trim()}
```
Notes: Project-wide convention. Nearly every component applies .replace(/\s+/g, " ").trim() to handle conditional class concatenation.
