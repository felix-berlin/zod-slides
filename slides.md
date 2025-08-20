---
theme: apple-basic
background: https://source.unsplash.com/1920x1080/\?code,typescript
class: text-center
highlighter: shiki
lineNumbers: false
info: |
  ## Zod - TypeScript-first Schema Validation

  Eine moderne Lösung für Type-Safe Validation in TypeScript
drawings:
  persist: false
transition: slide-left
title: Zod - TypeScript-first Schema Validation
mdc: true
---

# Zod

## TypeScript-first Schema Validation

<div class="flex items-center justify-center gap-8 my-8">
  <img src="/assets/ts-logo-512.svg" class="w-16 h-16" alt="TypeScript Logo" />
  <div class="text-4xl">+</div>
  <img src="/assets/logo-glow.webp" class="w-16 h-16" alt="Zod Logo" />
</div>

<div class="mt-6">
  Typsichere Validierung für moderne TypeScript-Anwendungen
</div>

<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    Weiter <carbon:arrow-right class="inline"/>
  </span>
</div>

<!--
Begrüßung und Einführung:
- Vorstellung: Heute sprechen wir über Zod
- Zielgruppe: TypeScript-Entwickler, die sich mit Validierung beschäftigen
- Dauer: ca. 10-15 Minuten
- Zod ist eine der populärsten Schema-Validation Libraries im TypeScript-Ökosystem
-->

---

transition: fade-out
---

# Was ist Zod?

<v-clicks>

- **TypeScript-first** Schema Validation Library
- **Zero Dependencies** - leichtgewichtig und performant
- **Type Inference** - automatische TypeScript-Typen aus Schemas
- **Runtime Validation** - sichere Validierung zur Laufzeit
- **Entwicklerfreundlich** - intuitive API und excellente DX

</v-clicks>

<!--
Key Points für diese Slide:
- Betone "TypeScript-first" - das ist der Hauptunterschied zu anderen Libraries
- Zero Dependencies erwähnen - wichtig für Bundle-Size
- Type Inference erklären - automatische Typen sind ein Killer-Feature
- Runtime + Compile-time Sicherheit hervorheben
- Frage ans Publikum: "Wer kennt andere Validation Libraries wie Joi oder Yup?"
-->

<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

---

# Das Problem ohne Zod

```typescript {all|1-5|7-11|13-17}
// Keine Typsicherheit bei API-Responses
function fetchUser(id: string): Promise<any> {
  return fetch(`/api/users/${id}`)
    .then(res => res.json())
}

// Manuelle Validierung - fehleranfällig
function validateUser(data: any): boolean {
  return typeof data.name === 'string' &&
         typeof data.email === 'string' &&
         typeof data.age === 'number'
}

// Keine automatischen TypeScript-Typen
const user = await fetchUser('123')
// user.name - TypeScript weiß nicht, was verfügbar ist
// user.nonExistent - Kein Fehler zur Compile-Zeit
```

<!--
Probleme hervorheben:
- any-Type überall - keine Typsicherheit
- Manuelle Validierung ist fehleranfällig und mühsam
- Keine automatische Type-Generierung
- Runtime-Fehler sind häufig
- Code-Duplikation zwischen Validation und Types
- Beispiel: "Was passiert wenn die API ein zusätzliches Feld zurückgibt?"
-->

---

# Die Zod-Lösung

```typescript {all|1-6|8-12|14-18}
import { z } from 'zod'

// Schema definieren
const UserSchema = z.object({
  name: z.string(),
  email: z.string().email(),
  age: z.number().positive()
})

// Automatische TypeScript-Typen
type User = z.infer<typeof UserSchema>
// User = { name: string, email: string, age: number }

// Typsichere Validierung
async function fetchUser(id: string): Promise<User> {
  const response = await fetch(`/api/users/${id}`)
  const data = await response.json()
  return UserSchema.parse(data) // Runtime validation + type safety
}
```

<!--
Zod-Vorteile demonstrieren:
- Ein Schema = automatische Types UND Validation
- z.infer<> zeigen - das ist der Magic Moment!
- .parse() wirft Fehler bei ungültigen Daten
- Keine Code-Duplikation mehr
- IntelliSense funktioniert perfekt
- Live-Demo: "Schauen wir uns z.infer in VS Code an"
-->

---

# Hauptvorteile von Zod

<div class="grid grid-cols-2 gap-4">

<div>

## 🔒 **Type Safety**

- Automatische TypeScript-Typen
- Compile-time und Runtime-Sicherheit
- Keine Type-Assertions nötig

## ⚡ **Performance**

- Zero Dependencies
- Kleine Bundle-Größe
- Schnelle Validierung

</div>

<div>

## 🛠️ **Developer Experience**

- Intuitive API
- Excellente Fehlermeldungen
- IntelliSense-Support

## 🔧 **Flexibilität**

- Composable Schemas
- Custom Validations
- Transformations

</div>

</div>

---

# Praktisches Beispiel

```typescript {all|1-8|10-16|18-22}
import { z } from 'zod'

// Komplexeres Schema
const CreatePostSchema = z.object({
  title: z.string().min(1).max(100),
  content: z.string().min(10),
  tags: z.array(z.string()).max(5),
  publishAt: z.date().optional()
})

// API Endpoint mit Validation
app.post('/posts', async (req, res) => {
  try {
    const validatedData = CreatePostSchema.parse(req.body)
    const post = await createPost(validatedData)
    res.json(post)
  } catch (error) {
    if (error instanceof z.ZodError) {
      return res.status(400).json({ errors: error.errors })
    }
    throw error
  }
})
```

---

# Erweiterte Features

<v-clicks>

- **Transformations**: Daten während der Validierung transformieren
- **Refinements**: Custom Validierungslogik
- **Unions & Discriminated Unions**: Komplexe Typen
- **Recursive Schemas**: Für verschachtelte Strukturen
- **Async Validations**: Für Datenbankabfragen etc.
- **Error Handling**: Detaillierte Fehlermeldungen

</v-clicks>

```typescript
const TransformSchema = z.string()
  .transform(val => val.trim())
  .refine(val => val.length > 0, "String cannot be empty")
```

---

# Warum Zod wählen?

<div class="grid grid-cols-1 gap-6">

<v-clicks>

<div class="p-4 border rounded">
  <h3 class="text-green-400">✅ Zod</h3>
  <ul>
    <li>TypeScript-first Design</li>
    <li>Automatische Type Inference</li>
    <li>Zero Dependencies</li>
    <li>Moderne, composable API</li>
    <li>Große Community & aktive Entwicklung</li>
  </ul>
</div>

<div class="p-4 border rounded opacity-60">
  <h3 class="text-gray-400">❌ Alternativen</h3>
  <ul>
    <li>Joi: Nicht TypeScript-first</li>
    <li>Yup: Veraltete API, schwächere TypeScript-Integration</li>
    <li>Manuelle Validierung: Fehleranfällig, mehr Code</li>
  </ul>
</div>

</v-clicks>

</div>

---

layout: center
class: text-center
---

# Fazit

Zod bietet die **beste Lösung** für typsichere Validierung in TypeScript-Projekten

<div class="pt-12">
  <span class="text-6xl">🎯</span>
</div>

**Type Safety + Developer Experience + Performance**

---

layout: center
class: text-center
---

# Danke

Fragen?

<div class="pt-12">
  <a href="https://github.com/colinhacks/zod" target="_blank" alt="Zod GitHub" class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon:logo-github />
  </a>
</div>

[github.com/colinhacks/zod](https://github.com/colinhacks/zod)
