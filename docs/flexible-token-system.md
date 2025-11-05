# Flexible Token System Architecture
## Design System Framework - Pillar 2 Core

**Version:** 2.0.0 (Flexible Levels)
**Date:** November 5, 2025
**Status:** Design Phase

---

## 🎯 Core Concept: Flexible Abstraction Levels

### **Key Principle:**
Users can define **1 to X levels** of token abstraction, with custom names for each level, based on their team's needs and design system complexity.

---

## 📊 Default Level Templates (Suggestions)

### **Template 1: Simple (2 Levels)** - For Small Projects
```
Level 1: Base Tokens
  └── Raw values: #2B7CD3, 16px, 1rem
      ↓
Level 2: Component Tokens
  └── button-primary-bg, heading-font-size
```

**Use case:** Small projects, MVP, startups

---

### **Template 2: Standard (3 Levels)** - Most Common
```
Level 1: Primitive Tokens
  └── Raw values: brand-blue-500, space-4
      ↓
Level 2: Semantic Tokens
  └── Purpose: color-primary, spacing-medium
      ↓
Level 3: Component Tokens
  └── Implementation: button-primary-background
```

**Use case:** Most design systems, recommended default

---

### **Template 3: Enterprise (4 Levels)** - Complex Systems
```
Level 1: Core Tokens
  └── Raw values: blue-500, spacing-base
      ↓
Level 2: System Tokens
  └── System-wide: color-interactive, space-component-gap
      ↓
Level 3: Pattern Tokens
  └── Pattern-specific: form-input-border, card-padding
      ↓
Level 4: Component Tokens
  └── Component-specific: button-primary-background-default
```

**Use case:** Large organizations, multiple products, complex systems

---

### **Template 4: Minimal (1 Level)** - Direct Mapping
```
Level 1: Tokens
  └── All tokens: button-bg-blue, text-size-large
```

**Use case:** Simple projects, prototypes, learning

---

### **Template 5: Advanced (5+ Levels)** - Maximum Flexibility
```
Level 1: Foundation
  └── #2B7CD3
      ↓
Level 2: Brand
  └── brand-primary
      ↓
Level 3: System
  └── color-interactive
      ↓
Level 4: Context
  └── form-field-active-border
      ↓
Level 5: Component
  └── input-text-border-focus
      ↓
Level 6: Variant (optional)
  └── input-text-large-border-focus-error
```

**Use case:** Design system teams, agencies managing multiple brands

---

## 🏗️ Data Model (Flexible)

### **Level Definition**
```typescript
interface TokenLevel {
  id: string;
  order: number;                    // 1, 2, 3, etc.
  name: string;                     // "Primitive", "Semantic", custom name
  description: string;              // What this level represents
  canReferenceLevel: number | null; // Which level(s) can this reference? null = no references (raw values)
  isRenameable: boolean;            // Can users rename this level?
  icon?: string;                    // Visual identifier
  color?: string;                   // UI color coding
}
```

**Example:**
```typescript
const level1: TokenLevel = {
  id: "lvl-1",
  order: 1,
  name: "Primitive Tokens",        // User can change to "Base", "Foundation", etc.
  description: "Raw design values",
  canReferenceLevel: null,          // Level 1 has raw values only
  isRenameable: true,
  color: "#3b82f6"                  // Blue
};

const level2: TokenLevel = {
  id: "lvl-2",
  order: 2,
  name: "Semantic Tokens",         // User can change to "System", "Purpose", etc.
  description: "Purpose-driven tokens",
  canReferenceLevel: 1,            // Can reference Level 1
  isRenameable: true,
  color: "#10b981"                 // Green
};

const level3: TokenLevel = {
  id: "lvl-3",
  order: 3,
  name: "Component Tokens",        // User can change to "Implementation", etc.
  description: "Component-specific tokens",
  canReferenceLevel: 2,            // Can reference Level 2
  isRenameable: true,
  color: "#8b5cf6"                 // Purple
};
```

---

### **Token Definition (Level-Agnostic)**
```typescript
interface Token {
  id: string;
  levelId: string;                 // Which level does this token belong to?
  name: string;                    // Token name

  // Value can be direct OR reference
  valueType: 'direct' | 'reference';
  directValue?: string;            // For Level 1: "#2B7CD3", "16px"
  referenceTokenId?: string;       // For Level 2+: references another token

  // Computed/inherited value
  computedValue: string;           // Final resolved value

  // Metadata
  property: 'color' | 'spacing' | 'typography' | 'border' | 'shadow' | 'other';
  category?: string;               // User-defined category
  description?: string;
  tags?: string[];

  // Relationships
  referencedBy: string[];          // Which tokens reference this one
  references?: string;             // Which token does this reference

  // Status
  status: 'draft' | 'active' | 'deprecated';

  // Timestamps
  createdAt: Date;
  updatedAt: Date;
}
```

**Example - Level 1 Token:**
```typescript
const primitiveToken: Token = {
  id: "tok-001",
  levelId: "lvl-1",
  name: "brand-blue-500",
  valueType: 'direct',
  directValue: "#2B7CD3",
  computedValue: "#2B7CD3",
  property: 'color',
  referencedBy: ["tok-020", "tok-021"],  // Semantic tokens referencing this
  status: 'active',
  createdAt: new Date(),
  updatedAt: new Date()
};
```

**Example - Level 2 Token:**
```typescript
const semanticToken: Token = {
  id: "tok-020",
  levelId: "lvl-2",
  name: "color-primary",
  valueType: 'reference',
  referenceTokenId: "tok-001",     // References brand-blue-500
  computedValue: "#2B7CD3",        // Inherited from tok-001
  property: 'color',
  referencedBy: ["tok-040"],       // Component token referencing this
  references: "tok-001",
  status: 'active',
  createdAt: new Date(),
  updatedAt: new Date()
};
```

**Example - Level 3 Token:**
```typescript
const componentToken: Token = {
  id: "tok-040",
  levelId: "lvl-3",
  name: "button-primary-background",
  valueType: 'reference',
  referenceTokenId: "tok-020",     // References color-primary
  computedValue: "#2B7CD3",        // Inherited through chain
  property: 'color',
  category: 'button',
  referencedBy: [],                // Nothing references this (final level)
  references: "tok-020",
  status: 'active',
  createdAt: new Date(),
  updatedAt: new Date()
};
```

---

## 🎨 User Interface Design

### **Level Management Screen**

```
┌─────────────────────────────────────────────────────────────┐
│ Token System Configuration                                  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  📊 Choose Your Token Structure                             │
│                                                             │
│  Templates:                                                 │
│  ○ Simple (2 levels)      - For small projects             │
│  ● Standard (3 levels)    - Recommended ⭐                  │
│  ○ Enterprise (4 levels)  - For complex systems            │
│  ○ Custom                 - Build your own                  │
│                                                             │
│  ┌───────────────────────────────────────────────────────┐ │
│  │ Current Setup: Standard (3 levels)                    │ │
│  │                                                       │ │
│  │ Level 1: [Primitive Tokens        ▼] 🟦 [Edit] [✕]  │ │
│  │   └─ Description: Raw design values                  │ │
│  │                                                       │ │
│  │ Level 2: [Semantic Tokens         ▼] 🟩 [Edit] [✕]  │ │
│  │   └─ Description: Purpose-driven tokens              │ │
│  │   └─ References: Level 1                             │ │
│  │                                                       │ │
│  │ Level 3: [Component Tokens        ▼] 🟣 [Edit] [✕]  │ │
│  │   └─ Description: Component-specific                 │ │
│  │   └─ References: Level 2                             │ │
│  │                                                       │ │
│  │ [+ Add Another Level]                                │ │
│  └───────────────────────────────────────────────────────┘ │
│                                                             │
│  [Cancel]                            [Save Configuration]  │
└─────────────────────────────────────────────────────────────┘
```

---

### **Token Creation Modal**

```
┌─────────────────────────────────────────────────────────────┐
│ Create New Token                                   Level 2  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Token Name *                                               │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ color-primary                                        │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                             │
│  Property *                                                 │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ Color                                            ▼   │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                             │
│  Value Source (Level 2 tokens reference Level 1) *         │
│  ● Reference Level 1 Token                                  │
│  ○ Direct Value (not recommended for Level 2)              │
│                                                             │
│  Select Level 1 Token:                                      │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ 🔵 brand-blue-500  →  #2B7CD3              [Select] │  │
│  │ 🔵 brand-blue-600  →  #1e5aa8              [Select] │  │
│  │ 🔵 neutral-gray-100 → #f3f4f6              [Select] │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                             │
│  Category (optional)                                        │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ Interactive                                          │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                             │
│  Description (optional)                                     │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ Primary interactive color for buttons, links         │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                             │
│  [Cancel]                                    [Create Token] │
└─────────────────────────────────────────────────────────────┘
```

---

## 🔄 Wizard Integration

### **After Wizard Completion:**

```
┌─────────────────────────────────────────────────────────────┐
│ 🎉 Design System Created!                                   │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Your design system foundations are ready:                  │
│  ✅ Colors defined (12 colors)                              │
│  ✅ Typography scale (8 sizes)                              │
│  ✅ Spacing system (10 values)                              │
│  ✅ Components selected (15 components)                     │
│                                                             │
│  ┌───────────────────────────────────────────────────────┐ │
│  │ 🎯 Set Up Your Token System                           │ │
│  │                                                       │ │
│  │ We recommend starting with Standard (3 levels):       │ │
│  │                                                       │ │
│  │ Level 1: Primitive Tokens                            │ │
│  │   └─ Your 12 colors, 8 font sizes, 10 spacing values │ │
│  │                                                       │ │
│  │ Level 2: Semantic Tokens                             │ │
│  │   └─ We'll suggest: color-primary, text-large, etc. │ │
│  │                                                       │ │
│  │ Level 3: Component Tokens                            │ │
│  │   └─ For your 15 components                          │ │
│  │                                                       │ │
│  │ [Skip for Now]  [Choose Different]  [✨ Set Up Now] │ │
│  └───────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

### **Auto-Population Logic:**

**If user clicks "Set Up Now":**

1. **Create Level Structure** (based on template or custom choice)

2. **Populate Level 1** from wizard data:
   ```typescript
   // From wizard colors
   {
     name: "brand-primary",
     value: "#2B7CD3",
     property: "color"
   },
   {
     name: "brand-secondary",
     value: "#8B5CF6",
     property: "color"
   },

   // From wizard typography
   {
     name: "font-size-base",
     value: "16px",
     property: "typography"
   },

   // From wizard spacing
   {
     name: "space-4",
     value: "16px",
     property: "spacing"
   }
   ```

3. **Suggest Level 2 Tokens** (AI or rule-based):
   ```typescript
   {
     name: "color-interactive",
     references: "brand-primary",
     description: "Use for interactive elements"
   },
   {
     name: "text-body",
     references: "font-size-base",
     description: "Default body text size"
   }
   ```

4. **Create Component Token Placeholders** for selected components:
   ```typescript
   {
     name: "button-primary-background",
     references: "color-interactive",
     component: "button-primary"
   }
   ```

---

## 🔍 Token Relationship Visualization

### **Token Detail View:**

```
┌─────────────────────────────────────────────────────────────┐
│ Token: color-primary                           Level 2 🟩   │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Value: #2B7CD3                                             │
│  Property: Color                                            │
│  Status: Active ✅                                          │
│                                                             │
│  ┌───────────────────────────────────────────────────────┐ │
│  │ Relationship Chain                                    │ │
│  │                                                       │ │
│  │  Level 1: brand-blue-500 (#2B7CD3)                   │ │
│  │      ↓ references                                     │ │
│  │  Level 2: color-primary (#2B7CD3) ← YOU ARE HERE     │ │
│  │      ↓ referenced by                                  │ │
│  │  Level 3: button-primary-background (#2B7CD3)        │ │
│  │  Level 3: link-color-default (#2B7CD3)               │ │
│  │  Level 3: focus-ring-color (#2B7CD3)                 │ │
│  └───────────────────────────────────────────────────────┘ │
│                                                             │
│  Impact Analysis:                                           │
│  ⚠️ 12 component tokens depend on this                      │
│  ⚠️ Changing this will affect 23 components                 │
│                                                             │
│  [Edit Token]  [Duplicate]  [Delete]  [View All Uses]      │
└─────────────────────────────────────────────────────────────┘
```

---

## 🎛️ Level Management Features

### **Rename Level:**
```typescript
function renameLevel(levelId: string, newName: string) {
  // Update level name
  // No impact on tokens - they still reference by levelId
  // Update UI labels
}
```

### **Add New Level:**
```typescript
function addLevel(position: 'above' | 'below', existingLevelOrder: number) {
  // Insert new level at specified position
  // Update order numbers for subsequent levels
  // New level can reference level above it
}
```

### **Delete Level:**
```typescript
function deleteLevel(levelId: string) {
  // Check if any tokens exist at this level
  if (hasTokens) {
    // Warn user: "12 tokens exist at this level. Delete or move them first."
    return;
  }

  // Remove level
  // Update orders
}
```

### **Reorder Levels:**
```typescript
function reorderLevels(newOrder: string[]) {
  // Validate: ensure reference chain is valid
  // Level N can only reference Level N-1

  // Update orders
  // Recalculate computed values
}
```

---

## 📤 Export with Flexible Levels

### **Export Adapts to Structure:**

**For 3-level system:**
```json
{
  "levels": [
    { "name": "Primitive", "order": 1 },
    { "name": "Semantic", "order": 2 },
    { "name": "Component", "order": 3 }
  ],
  "tokens": {
    "primitive": {
      "brand-blue-500": "#2B7CD3"
    },
    "semantic": {
      "color-primary": "{primitive.brand-blue-500}"
    },
    "component": {
      "button-primary-bg": "{semantic.color-primary}"
    }
  }
}
```

**For 2-level system:**
```json
{
  "levels": [
    { "name": "Base", "order": 1 },
    { "name": "Component", "order": 2 }
  ],
  "tokens": {
    "base": {
      "blue-500": "#2B7CD3"
    },
    "component": {
      "button-primary-bg": "{base.blue-500}"
    }
  }
}
```

---

## 🎯 Key Questions for You:

1. **Default Template:** Should "Standard (3 levels)" be the default suggestion after wizard?

2. **Level Names:** Are these good default names, or do you prefer others?
   - Primitive / Semantic / Component
   - Base / Purpose / Implementation
   - Foundation / System / Component
   - Core / Context / Application

3. **Minimum Levels:** Should we enforce at least 1 level, or allow 0 (no token system)?

4. **Maximum Levels:** Is there a practical maximum? (5-6 seems reasonable)

5. **Level Validation:** Should we prevent deleting a level that has tokens referencing it?

---

## 💾 Database Schema

```sql
-- Levels table
CREATE TABLE token_levels (
  id UUID PRIMARY KEY,
  project_id UUID REFERENCES projects(id),
  order_number INTEGER NOT NULL,
  name VARCHAR(100) NOT NULL,
  description TEXT,
  can_reference_level_id UUID REFERENCES token_levels(id),
  color VARCHAR(7),
  created_at TIMESTAMP,
  updated_at TIMESTAMP
);

-- Tokens table
CREATE TABLE tokens (
  id UUID PRIMARY KEY,
  project_id UUID REFERENCES projects(id),
  level_id UUID REFERENCES token_levels(id),
  name VARCHAR(255) NOT NULL,
  value_type VARCHAR(20), -- 'direct' or 'reference'
  direct_value TEXT,
  reference_token_id UUID REFERENCES tokens(id),
  computed_value TEXT,
  property VARCHAR(50),
  category VARCHAR(100),
  description TEXT,
  status VARCHAR(20),
  created_at TIMESTAMP,
  updated_at TIMESTAMP,

  CONSTRAINT token_value_check CHECK (
    (value_type = 'direct' AND direct_value IS NOT NULL) OR
    (value_type = 'reference' AND reference_token_id IS NOT NULL)
  )
);

-- Token relationships (for querying)
CREATE TABLE token_relationships (
  token_id UUID REFERENCES tokens(id),
  references_token_id UUID REFERENCES tokens(id),
  PRIMARY KEY (token_id, references_token_id)
);
```

---

**This is the flexible system you wanted!** What do you think? Should I refine anything? 🚀
