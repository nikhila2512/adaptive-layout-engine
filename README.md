# Adaptive Layout Engine

## About the Project

This project is a small adaptive layout engine for displaying the same advertisement across different screen sizes and environments.

The main idea is to define the advertisement once and allow the layout engine to calculate a suitable arrangement based on the constraints of the selected surface.

The project is built using TypeScript and React.

The demonstration uses a gaming advertisement containing:

* Headline
* Product image
* CTA button
* Price
* Logo

The same advertisement is tested across multiple surfaces with different dimensions and constraints.

---

## What I Built

The project contains the following main parts:

* A declarative advertisement specification
* Surface profiles with layout constraints
* A priority-based layout resolver
* Constraint-aware element placement
* Dropped-element reporting
* DOM/CSS rendering
* React-based demonstration interface
* TypeScript validation for the advertisement specification

The layout resolver is responsible for making placement decisions. CSS is used to display the resolved result rather than defining separate layouts for each surface.

---

## Supported Surfaces

The application currently supports five surfaces:

| Surface               |  Dimensions | Result               |
| --------------------- | ----------: | -------------------- |
| Mobile Portrait       |   320 × 480 | 5 placed / 0 dropped |
| Mobile Landscape      |   480 × 320 | 4 placed / 1 dropped |
| Broadcast Lower Third |  1920 × 250 | 5 placed / 0 dropped |
| Square Retail Kiosk   | 1080 × 1080 | 5 placed / 0 dropped |
| Compact Banner        |   300 × 120 | 2 placed / 3 dropped |

The Compact Banner is intentionally constrained to demonstrate how the engine handles insufficient space.

---

## Advertisement Elements

The advertisement specification contains the following elements:

| Element       | Type   | Role      | Priority |
| ------------- | ------ | --------- | -------: |
| Headline      | Text   | Primary   |        1 |
| Product Image | Image  | Hero      |        1 |
| CTA           | Button | Action    |        2 |
| Price         | Text   | Secondary |        2 |
| Logo          | Image  | Branding  |        3 |

The priority value determines the importance of an element during layout resolution.

Lower priority numbers represent more important content.

---

## Element Minimum Sizes

Each advertisement element can define minimum dimensions.

| Element       | Minimum Width | Minimum Height |
| ------------- | ------------: | -------------: |
| Headline      |         180px |           50px |
| Product Image |         140px |          140px |
| CTA           |         100px |           44px |
| Logo          |          60px |           40px |
| Price         |          80px |           40px |

These values are used by the resolver when checking whether an element can fit in the available space.

---

## How the Layout Resolver Works

The resolver uses a priority-based greedy placement strategy.

The process is:

1. Read the advertisement specification.
2. Read the selected surface constraints.
3. Determine the available area using the surface dimensions and safe area.
4. Process advertisement elements according to their priority.
5. Check the minimum size requirements of each element.
6. Search for a valid position inside the surface.
7. Check whether the position overlaps an already placed element.
8. Place the element if a valid position is found.
9. Drop the element if no valid position can be found.
10. Record the reason for dropped elements.

This allows the same advertisement specification to adapt to different surfaces.

---

## Constraint Handling

The resolver considers constraints such as:

* Surface width
* Surface height
* Safe area
* Minimum element width
* Minimum element height
* Minimum tap target
* Element priority
* Element overlap
* Surface boundaries

Elements cannot be placed outside the valid surface area.

Elements also cannot overlap elements that have already been placed.

When the available space is insufficient, lower-priority elements may be dropped instead of producing an invalid layout.

---

## Priority and Degradation

The project demonstrates graceful degradation on smaller surfaces.

For example, the Compact Banner has dimensions of:

```text
300 × 120
```

The current result is:

```text
Placed: 2
Dropped: 3
```

The dropped elements are:

* Product Image — Priority 1
* Price — Priority 2
* Logo — Priority 3

The resolver drops these elements because no valid non-overlapping position satisfies the surface constraints.

The application displays the element ID, priority, and reason for each dropped element.

---

## Project Structure

```text
adaptive-layout-assignment/
│
├── src/
│   ├── spec.ts
│   ├── surfaces.ts
│   ├── resolver.ts
│   ├── render-dom.ts
│   └── App.tsx
│
├── package.json
├── tsconfig.json
├── README.md
└── ARCHITECTURE.md
```

### `spec.ts`

Defines the advertisement elements and their properties.

### `surfaces.ts`

Defines the supported surfaces and their constraints.

### `resolver.ts`

Contains the layout resolution algorithm.

### `render-dom.ts`

Renders the resolved layout using DOM and CSS.

### `App.tsx`

Contains the React demonstration interface.

### `ARCHITECTURE.md`

Describes the architecture and separation of responsibilities in more detail.

---

## TypeScript Design

The project uses TypeScript to provide strong typing for the advertisement specification.

Supported element types are defined using union types:

```ts
type ElementType = "text" | "image" | "button";
```

Element roles are also represented using TypeScript union types.

The main interfaces include:

```text
AdElement
AdSpec
```

Runtime validation is also performed through the `defineAd()` function.

The validation checks:

* The advertisement contains at least one element.
* Element IDs are unique.
* Priority values are greater than zero.
* Minimum width values are valid.
* Minimum height values are valid.

This helps prevent invalid advertisement specifications from being passed to the resolver.

---

## Setup

### 1. Install Dependencies

Open a terminal inside the project folder and run:

```bash
npm install
```

### 2. Start the Development Server

Run:

```bash
npm run dev
```

The development server will provide a local URL that can be opened in the browser.

### 3. Build the Project

To create a production build, run:

```bash
npm run build
```

The project currently builds successfully without TypeScript or build errors.

---

## Using the Demo

After starting the development server:

1. Open the application in the browser.
2. Select a surface from the available surface options.
3. Observe how the same advertisement is resolved for that surface.
4. Check the number of placed elements.
5. Check the number of dropped elements.
6. Review the displayed surface constraints.
7. Select the Compact Banner to observe the degradation behavior.

The layout is recalculated when the selected surface changes.

---

## Example Results

### Mobile Portrait

```text
320 × 480
5 placed
0 dropped
```

### Mobile Landscape

```text
480 × 320
4 placed
1 dropped
```

### Broadcast Lower Third

```text
1920 × 250
5 placed
0 dropped
```

### Square Retail Kiosk

```text
1080 × 1080
5 placed
0 dropped
```

### Compact Banner

```text
300 × 120
2 placed
3 dropped
```

These results demonstrate that the same advertisement can produce different layouts depending on the available space and surface constraints.

---

## Validation and Testing

The advertisement specification includes runtime validation.

The following invalid cases are checked:

* Empty advertisement specification
* Duplicate element IDs
* Invalid priority values
* Invalid minimum width values
* Invalid minimum height values

The project was also tested across all supported surfaces to verify that elements are either placed within the valid area or reported as dropped.

The production build was tested using:

```bash
npm run build
```

---

## Known Limitations

The current implementation focuses on demonstrating the core adaptive layout concept.

Known limitations include:

* The resolver uses a greedy placement strategy.
* It does not perform a globally optimal placement search.
* Browser-based text measurement is not currently implemented.
* The number of supported element types is limited.
* Image handling is basic.
* Animation is not currently implemented.
* A Canvas renderer is not currently implemented.
* Advanced typography adaptation is not currently implemented.

These limitations could be addressed in future versions.

---

## Future Improvements

Possible improvements include:

* More advanced constraint-solving algorithms
* Browser-based text measurement
* Automatic font-size adjustment
* Better image scaling and cropping
* Additional element types
* Canvas rendering support
* Animation support
* Accessibility improvements
* More advanced placement optimization
* Support for additional unknown surfaces

---

## Architecture

The application separates the following responsibilities:

* Advertisement definition
* Surface constraints
* Layout calculation
* Rendering
* User interface

The resolver is independent of React and receives the advertisement specification and surface profile as inputs.

The renderer receives the resolved layout and is responsible for displaying it.

This separation makes the system easier to maintain and allows the layout engine to potentially support different rendering backends in the future.

More details are available in:

```text
ARCHITECTURE.md
```

---

## AI Usage Disclosure

AI tools were used during the development of this assignment for:

* Understanding the assignment requirements
* Exploring implementation approaches
* Debugging and troubleshooting
* Improving documentation
* Reviewing the project structure

The final implementation was reviewed and tested during development, including testing the supported surfaces and production build.

---

## Time Spent

Approximately 3–5 days were spent designing, implementing, testing, and documenting the project.

---

## Assignment Status

The core adaptive layout engine has been implemented and tested.

The project demonstrates:

* Declarative advertisement specification
* Multiple surface profiles
* Priority-based layout resolution
* Constraint-aware placement
* Overlap prevention
* Graceful degradation
* Dropped-element reporting
* TypeScript type safety
* DOM/CSS rendering
* React demonstration interface

The project is ready for repository submission after the final GitHub repository setup and review.
