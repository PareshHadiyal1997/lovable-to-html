# TSX → HTML/CSS/JS Patterns Reference

Extended cheat sheet for converting React/TSX patterns to static HTML equivalents.

---

## HTML Attribute Conversions

| TSX (JSX) | HTML |
|-----------|------|
| `className="..."` | `class="..."` |
| `htmlFor="id"` | `for="id"` |
| `onClick={handler}` | `onclick="handler()"` |
| `onChange={handler}` | `oninput="handler()"` |
| `onSubmit={handler}` | `onsubmit="handler(); return false;"` |
| `style={{ color: 'red' }}` | `style="color: red;"` |
| `tabIndex={0}` | `tabindex="0"` |
| `readOnly` | `readonly` |
| `defaultValue="x"` | `value="x"` |
| `autoFocus` | `autofocus` |
| `{...spread}` | Expand each prop individually |

---

## Shadcn/UI Component Conversions

### Button
```tsx
// TSX
<Button variant="default" size="sm" onClick={fn}>Click me</Button>
<Button variant="outline">Outlined</Button>
<Button disabled>Disabled</Button>
```
```html
<!-- HTML -->
<button class="inline-flex items-center justify-center rounded-md text-sm font-medium h-9 px-3 bg-primary text-primary-foreground hover:bg-primary/90" onclick="fn()">Click me</button>
<button class="inline-flex items-center justify-center rounded-md border border-input bg-background hover:bg-accent text-sm font-medium h-10 px-4 py-2">Outlined</button>
<button class="inline-flex items-center justify-center rounded-md text-sm font-medium h-10 px-4 py-2 opacity-50 cursor-not-allowed" disabled>Disabled</button>
```

### Card
```tsx
// TSX
<Card>
  <CardHeader>
    <CardTitle>Title</CardTitle>
    <CardDescription>Description</CardDescription>
  </CardHeader>
  <CardContent>Content here</CardContent>
  <CardFooter>Footer</CardFooter>
</Card>
```
```html
<!-- HTML -->
<div class="rounded-lg border bg-card text-card-foreground shadow-sm">
  <div class="flex flex-col space-y-1.5 p-6">
    <h3 class="text-2xl font-semibold leading-none tracking-tight">Title</h3>
    <p class="text-sm text-muted-foreground">Description</p>
  </div>
  <div class="p-6 pt-0">Content here</div>
  <div class="flex items-center p-6 pt-0">Footer</div>
</div>
```

### Input
```tsx
<Input type="text" placeholder="Enter value" className="..." />
```
```html
<input type="text" placeholder="Enter value" class="flex h-10 w-full rounded-md border border-input bg-background px-3 py-2 text-sm ring-offset-background placeholder:text-muted-foreground focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring ..." />
```

### Badge
```tsx
<Badge variant="secondary">Label</Badge>
```
```html
<span class="inline-flex items-center rounded-full border px-2.5 py-0.5 text-xs font-semibold bg-secondary text-secondary-foreground">Label</span>
```

### Avatar
```tsx
<Avatar>
  <AvatarImage src="/avatar.jpg" alt="User" />
  <AvatarFallback>JD</AvatarFallback>
</Avatar>
```
```html
<span class="relative flex h-10 w-10 shrink-0 overflow-hidden rounded-full">
  <img class="aspect-square h-full w-full" src="/avatar.jpg" alt="User" />
  <!-- Fallback shown when image fails: -->
  <!-- <span class="flex h-full w-full items-center justify-center rounded-full bg-muted">JD</span> -->
</span>
```

### Dialog/Modal
```tsx
// TSX
const [open, setOpen] = useState(false);
<Dialog open={open} onOpenChange={setOpen}>
  <DialogTrigger asChild><Button>Open</Button></DialogTrigger>
  <DialogContent>
    <DialogHeader><DialogTitle>Title</DialogTitle></DialogHeader>
    <p>Content</p>
  </DialogContent>
</Dialog>
```
```html
<!-- HTML -->
<button onclick="document.getElementById('dialog-1').classList.remove('hidden')">Open</button>

<div id="dialog-1" class="hidden fixed inset-0 z-50 flex items-center justify-center">
  <!-- Backdrop -->
  <div class="fixed inset-0 bg-black/80" onclick="document.getElementById('dialog-1').classList.add('hidden')"></div>
  <!-- Dialog -->
  <div class="relative z-50 bg-background rounded-lg shadow-lg p-6 w-full max-w-md mx-4">
    <h2 class="text-lg font-semibold leading-none tracking-tight">Title</h2>
    <p>Content</p>
    <button class="absolute right-4 top-4" onclick="document.getElementById('dialog-1').classList.add('hidden')">✕</button>
  </div>
</div>
```

### Tabs
```tsx
// TSX
const [tab, setTab] = useState('tab1');
<Tabs value={tab} onValueChange={setTab}>
  <TabsList>
    <TabsTrigger value="tab1">Tab 1</TabsTrigger>
    <TabsTrigger value="tab2">Tab 2</TabsTrigger>
  </TabsList>
  <TabsContent value="tab1">Content 1</TabsContent>
  <TabsContent value="tab2">Content 2</TabsContent>
</Tabs>
```
```html
<!-- HTML -->
<div class="tabs-container">
  <div class="inline-flex h-10 items-center justify-center rounded-md bg-muted p-1">
    <button class="tab-trigger active" onclick="switchTab('tab1', this)">Tab 1</button>
    <button class="tab-trigger" onclick="switchTab('tab2', this)">Tab 2</button>
  </div>
  <div id="tab1" class="tab-content">Content 1</div>
  <div id="tab2" class="tab-content hidden">Content 2</div>
</div>
```
```js
// JS
function switchTab(tabId, triggerEl) {
  document.querySelectorAll('.tab-content').forEach(el => el.classList.add('hidden'));
  document.querySelectorAll('.tab-trigger').forEach(el => el.classList.remove('active'));
  document.getElementById(tabId).classList.remove('hidden');
  triggerEl.classList.add('active');
}
```

### Select / Dropdown
```tsx
<Select value={val} onValueChange={setVal}>
  <SelectTrigger><SelectValue placeholder="Choose..." /></SelectTrigger>
  <SelectContent>
    <SelectItem value="a">Option A</SelectItem>
    <SelectItem value="b">Option B</SelectItem>
  </SelectContent>
</Select>
```
```html
<select class="flex h-10 w-full items-center justify-between rounded-md border border-input bg-background px-3 py-2 text-sm">
  <option value="">Choose...</option>
  <option value="a">Option A</option>
  <option value="b">Option B</option>
</select>
```

### Checkbox
```tsx
<Checkbox id="check" checked={checked} onCheckedChange={setChecked} />
<Label htmlFor="check">Accept terms</Label>
```
```html
<input type="checkbox" id="check" class="h-4 w-4 rounded border border-primary" />
<label for="check" class="text-sm font-medium leading-none">Accept terms</label>
```

### Switch (Toggle)
```tsx
<Switch checked={on} onCheckedChange={setOn} />
```
```html
<button role="switch" aria-checked="false" id="switch-1"
  class="peer inline-flex h-6 w-11 shrink-0 cursor-pointer items-center rounded-full border-2 border-transparent bg-input transition-colors"
  onclick="this.setAttribute('aria-checked', this.getAttribute('aria-checked') === 'true' ? 'false' : 'true'); this.classList.toggle('bg-primary')">
  <span class="pointer-events-none block h-5 w-5 rounded-full bg-background shadow-lg ring-0 transition-transform translate-x-0 peer-aria-checked:translate-x-5"></span>
</button>
```

### Accordion
```tsx
<Accordion type="single" collapsible>
  <AccordionItem value="item-1">
    <AccordionTrigger>Question?</AccordionTrigger>
    <AccordionContent>Answer here.</AccordionContent>
  </AccordionItem>
</Accordion>
```
```html
<div class="accordion">
  <div class="accordion-item border-b">
    <button class="accordion-trigger flex flex-1 items-center justify-between py-4 font-medium"
      onclick="toggleAccordion(this)">
      Question?
      <svg class="h-4 w-4 transition-transform" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="6 9 12 15 18 9"></polyline></svg>
    </button>
    <div class="accordion-content hidden pb-4 pt-0">Answer here.</div>
  </div>
</div>
```
```js
function toggleAccordion(trigger) {
  const content = trigger.nextElementSibling;
  const icon = trigger.querySelector('svg');
  content.classList.toggle('hidden');
  icon.style.transform = content.classList.contains('hidden') ? '' : 'rotate(180deg)';
}
```

### Sheet / Sidebar Drawer
```tsx
<Sheet>
  <SheetTrigger>Open</SheetTrigger>
  <SheetContent side="right">...</SheetContent>
</Sheet>
```
```html
<button onclick="document.getElementById('sheet-1').classList.remove('translate-x-full')">Open</button>
<div id="sheet-1" class="fixed inset-y-0 right-0 z-50 h-full w-3/4 sm:max-w-sm bg-background shadow-lg transition-transform translate-x-full p-6">
  <!-- Sheet content -->
  <button onclick="document.getElementById('sheet-1').classList.add('translate-x-full')" class="absolute right-4 top-4">✕</button>
</div>
<div class="fixed inset-0 z-40 bg-black/80 hidden" id="sheet-backdrop-1"
  onclick="document.getElementById('sheet-1').classList.add('translate-x-full'); this.classList.add('hidden')"></div>
```

---

## Lucide React Icon Conversions

```tsx
import { Home, Settings, User, Search, X, ChevronDown } from 'lucide-react';

// TSX usage
<Home className="w-5 h-5 text-gray-500" />
<Search className="w-4 h-4" />
```
```html
<!-- Requires <script src="https://unpkg.com/lucide@latest/dist/umd/lucide.min.js"></script> -->
<i data-lucide="home" class="w-5 h-5 text-gray-500"></i>
<i data-lucide="search" class="w-4 h-4"></i>
<!-- Note: lucide icon names use kebab-case: ChevronDown → chevron-down -->
```
```js
// Initialize once in DOMContentLoaded:
lucide.createIcons();
```

Common icon name mappings (React → Lucide HTML attribute):
- `ChevronDown` → `chevron-down`
- `ChevronRight` → `chevron-right`
- `ArrowLeft` → `arrow-left`
- `ExternalLink` → `external-link`
- `MoreVertical` → `more-vertical`
- `PlusCircle` → `plus-circle`

---

## Framer Motion → CSS Transitions

```tsx
// Fade in on mount
<motion.div initial={{ opacity: 0 }} animate={{ opacity: 1 }} transition={{ duration: 0.3 }}>
```
```css
/* CSS */
.fade-in {
  animation: fadeIn 0.3s ease forwards;
}
@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}
```
```html
<div class="fade-in">...</div>
```

```tsx
// Slide up
<motion.div initial={{ y: 20, opacity: 0 }} animate={{ y: 0, opacity: 1 }}>
```
```css
.slide-up {
  animation: slideUp 0.3s ease forwards;
}
@keyframes slideUp {
  from { transform: translateY(20px); opacity: 0; }
  to { transform: translateY(0); opacity: 1; }
}
```

---

## React Hook Form → Plain HTML Forms

```tsx
// TSX
const { register, handleSubmit } = useForm();
<form onSubmit={handleSubmit(onSubmit)}>
  <input {...register('email', { required: true })} />
  <button type="submit">Submit</button>
</form>
```
```html
<!-- HTML -->
<form onsubmit="handleSubmit(event)">
  <input type="email" name="email" required />
  <button type="submit">Submit</button>
</form>
```
```js
function handleSubmit(e) {
  e.preventDefault();
  const data = Object.fromEntries(new FormData(e.target));
  console.log('Form data:', data);
  // TODO: Connect to API
}
```

---

## Tailwind Color Palette Reference

When converting Tailwind color classes to hex (default palette):

```
gray-50: #f9fafb    gray-100: #f3f4f6   gray-200: #e5e7eb
gray-300: #d1d5db   gray-400: #9ca3af   gray-500: #6b7280
gray-600: #4b5563   gray-700: #374151   gray-800: #1f2937
gray-900: #111827   gray-950: #030712

blue-50: #eff6ff    blue-100: #dbeafe   blue-500: #3b82f6
blue-600: #2563eb   blue-700: #1d4ed8

green-500: #22c55e  green-600: #16a34a
red-500: #ef4444    red-600: #dc2626
yellow-500: #eab308 orange-500: #f97316
purple-500: #a855f7 pink-500: #ec4899
indigo-500: #6366f1 teal-500: #14b8a6
```

Tailwind spacing scale (1 unit = 0.25rem = 4px):
- `p-1` = 4px, `p-2` = 8px, `p-3` = 12px, `p-4` = 16px
- `p-5` = 20px, `p-6` = 24px, `p-8` = 32px, `p-10` = 40px
- `p-12` = 48px, `p-16` = 64px, `p-20` = 80px, `p-24` = 96px

---

## React Router → Multi-Page HTML

```tsx
// App.tsx with React Router
<Routes>
  <Route path="/" element={<HomePage />} />
  <Route path="/dashboard" element={<DashboardPage />} />
  <Route path="/settings" element={<SettingsPage />} />
  <Route path="/profile/:id" element={<ProfilePage />} />
</Routes>
```
Output files:
- `/` → `index.html`
- `/dashboard` → `dashboard.html`
- `/settings` → `settings.html`
- `/profile/:id` → `profile.html` (static example with sample data)

Navigation links:
```tsx
<Link to="/dashboard">Dashboard</Link>
```
```html
<a href="dashboard.html">Dashboard</a>
```

---

## Context / State Management → Static Data

When React components use context or state management for data:

```tsx
// TSX - pulls from context
const { user } = useUser();
const { products } = useProductsContext();
```
```html
<!-- HTML - use representative static data -->
<!-- User: John Doe, john@example.com -->
<!-- Products: 3 sample items shown -->
<!-- TODO: Connect to your data source / API -->
```

In JS, you can define static placeholder data:
```js
const SAMPLE_USER = { name: 'John Doe', email: 'john@example.com', avatar: null };
const SAMPLE_PRODUCTS = [
  { id: 1, name: 'Product One', price: 29.99, category: 'Electronics' },
  { id: 2, name: 'Product Two', price: 49.99, category: 'Clothing' },
  { id: 3, name: 'Product Three', price: 19.99, category: 'Books' },
];
```
