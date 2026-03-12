# claudeSpec Prompts

Ready-to-use prompts for generating design system documentation with Claude. Copy, paste, and replace the placeholder values with your own.

---

## All Specs (the full suite)

```
Create the following specs for this button component: [FIGMA_COMPONENT_URL]

1. **Anatomy** — Show all three button types (Primary, Secondary, Tertiary) side by side
   in the preview area with numbered markers and an annotation table. Toggle both icon
   slots visible so all elements are annotated.
2. **API** — Document all configurable properties with values, defaults, and configuration
   examples. Hover, Press, and Focus are transient interaction states handled at runtime.
3. **Color Annotation** — Map design tokens for Primary, Secondary, and Tertiary types
   across Brand and Destructive tones. Place live button instances in both light and dark
   preview placeholders.
4. **Structure** — Show spacing, padding, and sizing across Small, Medium, and Large sizes
   with token names.
5. **Screen Reader** — Cover Default, Disabled, and Focus states with VoiceOver, TalkBack,
   and ARIA specs. Place a live button instance in each state's preview placeholder.
6. **Properties** — Show variant axes (Type, Tone, Size) and boolean icon toggles with
   live instance previews, labeled.

The button has Type (Primary/Secondary/Tertiary), Tone (Brand/Neutral/Destructive/Inverse),
State (Default/Hover/Press/Focus/Disabled), and Size (Small/Medium/Large) variants with
optional left and right icons controlled by boolean toggles with instance swap slots.

My template configuration:
- fontFamily: [FONT_FAMILY]
- anatomyOverview: [ANATOMY_KEY]
- apiOverview: [API_KEY]
- colorAnnotation: [COLOR_KEY]
- structureSpec: [STRUCTURE_KEY]
- screenReader: [SCREEN_READER_KEY]
- propertyOverview: [PROPERTY_KEY]

Place each spec on the [PAGE_NAME] page. Use smart placement to avoid overlapping
existing content. When all specs are complete, tidy them up alphabetically with
100px gutters.
```

---

## Individual Prompts

### Anatomy

```
Create an anatomy spec for this component: [FIGMA_COMPONENT_URL]

Show all [VARIANT_AXIS] variants ([LIST_VARIANTS]) side by side in the preview area
with numbered markers and an annotation table. Toggle all optional element slots
visible so every element is annotated.

[COMPONENT_DESCRIPTION]

My template configuration:
- fontFamily: [FONT_FAMILY]
- anatomyOverview: [ANATOMY_KEY]

Use smart placement to avoid overlapping existing content on the page.
```

### API

```
Create an API spec for this component: [FIGMA_COMPONENT_URL]

Document all configurable properties with values, defaults, and configuration examples.
[LIST_TRANSIENT_STATES] are transient interaction states handled at runtime and should
be excluded from the API.

[COMPONENT_DESCRIPTION]

My template configuration:
- fontFamily: [FONT_FAMILY]
- apiOverview: [API_KEY]

Use smart placement to avoid overlapping existing content on the page.
```

### Color Annotation

```
Create a color annotation for this component: [FIGMA_COMPONENT_URL]

Map design tokens for [LIST_VARIANTS_TO_DOCUMENT] across [LIST_TONES_TO_DOCUMENT].
Place live component instances in both light and dark preview placeholders for each
variant section.

[COMPONENT_DESCRIPTION]

My template configuration:
- fontFamily: [FONT_FAMILY]
- colorAnnotation: [COLOR_KEY]

Use smart placement to avoid overlapping existing content on the page.
```

### Structure

```
Create a structure spec for this component: [FIGMA_COMPONENT_URL]

Show spacing, padding, sizing, corner radius, and gap values across [LIST_SIZE_VARIANTS]
with token names where available.

[COMPONENT_DESCRIPTION]

My template configuration:
- fontFamily: [FONT_FAMILY]
- structureSpec: [STRUCTURE_KEY]

Use smart placement to avoid overlapping existing content on the page.
```

### Screen Reader

```
Create a screen reader spec for this component: [FIGMA_COMPONENT_URL]

Cover [LIST_STATES] states with platform-specific tables for iOS VoiceOver, Android
TalkBack, and Web ARIA. Place a live component instance in each state's preview
placeholder showing the correct variant.

[COMPONENT_DESCRIPTION]

My template configuration:
- fontFamily: [FONT_FAMILY]
- screenReader: [SCREEN_READER_KEY]

Use smart placement to avoid overlapping existing content on the page.
```

### Properties

```
Create a properties spec for this component: [FIGMA_COMPONENT_URL]

Show each variant axis and boolean toggle as a visual exhibit with live instance
previews, labeled. Include [LIST_VARIANT_AXES] and [LIST_BOOLEAN_TOGGLES].

[COMPONENT_DESCRIPTION]

My template configuration:
- fontFamily: [FONT_FAMILY]
- propertyOverview: [PROPERTY_KEY]

Use smart placement to avoid overlapping existing content on the page.
```

---

## Tidy Up (standalone)

Use this anytime to clean up spec frames on the current page:

```
Please tidy up the spec frames on this page — arrange them alphabetically
with 100px gutters, top-aligned, to the right of any non-spec content.
```

---

## Placeholder Reference

| Placeholder | Example |
|---|---|
| `[FIGMA_COMPONENT_URL]` | `https://www.figma.com/design/abc123/File?node-id=123-456` |
| `[FONT_FAMILY]` | `Apparat` or `Inter` |
| `[ANATOMY_KEY]` | `747781c9cf5a5c0c0eab590cdbae6d4caa1b7f5b` |
| `[API_KEY]` | `7a2cd822aa204867962f519ea6d82b549718487b` |
| `[COLOR_KEY]` | `60c340263779a08555f2f32d2fe01c8eea17288a` |
| `[STRUCTURE_KEY]` | `e6ac3e2d76bd42e1fafa096960a0463a73a6afad` |
| `[SCREEN_READER_KEY]` | `af4ec5d498766b484451fc4e8895aa2020821632` |
| `[PROPERTY_KEY]` | `ea56726eee4986339b5e5884060c030b8c3df92e` |
| `[PAGE_NAME]` | `Button Demo` |
| `[COMPONENT_DESCRIPTION]` | `The button has Type (Primary/Secondary/Tertiary), Tone (Brand/Neutral/Destructive/Inverse)...` |
| `[LIST_TRANSIENT_STATES]` | `Hover, Press, and Focus` |
| `[LIST_VARIANTS_TO_DOCUMENT]` | `Primary, Secondary, and Tertiary types` |
| `[LIST_TONES_TO_DOCUMENT]` | `Brand and Destructive tones` |
| `[LIST_SIZE_VARIANTS]` | `Small, Medium, and Large` |
| `[LIST_STATES]` | `Default, Disabled, and Focus` |
| `[LIST_VARIANT_AXES]` | `Type, Tone, and Size` |
| `[LIST_BOOLEAN_TOGGLES]` | `Icon left and Icon right toggles` |

## Tips

- **Start a fresh conversation for each spec** — context is limited, and each spec is token-intensive
- **Include template keys in the prompt** — saves time vs. running setup each session
- **Be specific about which variants to show** — "Primary, Secondary, Tertiary" is better than "all types"
- **Name transient states explicitly** — helps Claude correctly exclude them from API docs
- **Run tidy-up as a final step** — or include it at the end of a multi-spec prompt
