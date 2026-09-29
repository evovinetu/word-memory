# Implementation Guide: Adding New Language to Word Memory

## Quick Start

To add a new language to the Word Memory Game, follow this step-by-step guide.

### Prerequisites
- Node.js installed
- pnpm package manager
- Basic understanding of Vue 3 and TypeScript
- UTF-8 text editor for word list creation

---

## Step 1: Choose Your Language

Select a language to add. Consider:
- **Word count**: Minimum 50 words recommended
- **Character set**: Ensure UTF-8 support
- **Grammar features**: Does it use articles? Genders? Special characters?

### Popular Language Choices
| Language | Code | Script | Articles | Notes |
|----------|------|--------|----------|-------|
| Spanish | `es` | Latin | Yes (el/la) | Similar structure to German |
| French | `fr` | Latin | Yes (le/la) | Accented characters |
| Portuguese | `pt` | Latin | Yes (o/a) | Brazilian variant popular |
| Italian | `it` | Latin | Yes (il/la) | Phonetic spelling |
| Polish | `pl` | Latin | No | Cases system |
| Swedish | `sv` | Latin | No | Special characters: å, ä, ö |

---

## Step 2: Create Word List

### Format Selection

**Option A: Simple words (no articles)**
```json
["word1", "word2", "word3"]
```

**Option B: Words with articles**
```json
[
  {"word": "palabra", "article": "la"},
  {"word": "casa", "article": "la"},
  {"word": "coche", "article": "el"}
]
```

### Word List Requirements
- Minimum 50 words for adequate game play
- Mix of common nouns and adjectives
-_variety in word lengths (2-15 characters)
- UTF-8 encoded

### Example: Spanish Word List (es.json)

```json
[
  {"word": "el", "article": "el"},
  {"word": "la", "article": "la"},
  {"word": "casa", "article": "la"},
  {"word": "perro", "article": "el"},
  {"word": "gato", "article": "el"},
  {"word": "mesa", "article": "la"},
  {"word": "silla", "article": "la"},
  {"word": "ventana", "article": "la"},
  {"word": "puerta", "article": "la"},
  {"word": "libro", "article": "el"},
  {"word": "lápiz", "article": "el"},
  {"word": "cuaderno", "article": "el"},
  {"word": "escuela", "article": "la"},
  {"word": "estudiante", "article": "el"},
  {"word": "maestro", "article": "el"},
  {"word": "amigo", "article": "el"},
  {"word": "familia", "article": "la"},
  {"word": "comida", "article": "la"},
  {"word": "agua", "article": "el"},
  {"word": "leche", "article": "la"},
  {"word": "pan", "article": "el"},
  {"word": "manzana", "article": "la"},
  {"word": "naranja", "article": "la"},
  {"word": "coche", "article": "el"},
  {"word": "auto", "article": "el"},
  {"word": "calle", "article": "la"},
  {"word": "ciudad", "article": "la"},
  {"word": "país", "article": "el"},
  {"word": "mundo", "article": "el"},
  {"word": "sol", "article": "el"},
  {"word": "luna", "article": "la"},
  {"word": "estrella", "article": "la"},
  {"word": "mar", "article": "el"},
  {"word": "río", "article": "el"},
  {"word": "montaña", "article": "la"},
  {"word": "flor", "article": "la"},
  {"word": "árbol", "article": "el"},
  {"word": "bird", "article": "el"},
  {"word": "pájaro", "article": "el"},
  {"word": "gaviota", "article": "la"},
  {"word": "mariposa", "article": "la"},
  {"word": "insecto", "article": "el"},
  {"word": "animal", "article": "el"},
  {"word": "persona", "article": "la"},
  {"word": "hombre", "article": "el"},
  {"word": "mujer", "article": "la"},
  {"word": "niño", "article": "el"},
  {"word": "niña", "article": "la"},
  {"word": "familia", "article": "la"},
  {"word": "amigo", "article": "el"},
  {"word": "amiga", "article": "la"}
]
```

---

## Step 3: Implementation Steps

### 3.1 Create Language File

**File**: `app/words/es.json`

```bash
# Navigate to project root
cd c:\AI\github-repos\word-memory

# Create the new language file with your word list
# (Use a text editor to create and save the JSON)
```

### 3.2 Update Word Pools Index

**File**: `app/words/index.ts`

```typescript
import en from './en.json'
import de from './de.json'
import ru from './ru.json'
import es from './es.json'  // Add this line

export type WordItem = string | { word: string; article?: string }

export const wordPools: Record<string, WordItem[]> = {
  en,
  de,
  ru,
  es  // Add this line
}

export default wordPools
```

### 3.3 Update Settings UI

**File**: `app/components/Settings.vue`

Add the new language option to the dropdown:

```vue
<div class="setting-group">
  <label for="language">Language</label>
  <div class="setting-input-wrap">
    <select id="language" v-model="language" class="setting-select">
      <option value="en">English</option>
      <option value="de">Deutsch</option>
      <option value="ru">Русский</option>
      <option value="es">Español</option>  <!-- Add this line -->
    </select>
  </div>
</div>
```

### 3.4 Update README

**File**: `README.md`

Update the language list:

```markdown
## 🌐 Supported Languages

- **English (en)**: Simple English words
- **Deutsch (de)**: German words with articles (der/die/das)
- **Русский (ru)**: Russian Cyrillic words
- **Español (es)**: Spanish words with articles (el/la) <!-- Add this line -->
```

---

## Step 4: Testing

### 4.1 Manual Testing

```bash
# Start development server
pnpm run dev
```

**Checklist**:
- [ ] Language appears in settings dropdown
- [ ] Selecting language changes word display
- [ ] Words show correctly during memorization phase
- [ ] Words show correctly in play grid
- [ ] Articles render (if applicable)
- [ ] Settings persist after page reload

### 4.2 Automated Testing

```bash
# Run tests
pnpm test
```

If tests fail due to language-specific assertions, update:
**File**: `app/tests/game.spec.ts`

---

## Step 5: Verification

### Common Issues & Solutions

**Issue**: Special characters display as squares/boxes
- **Solution**: Ensure file is UTF-8 encoded and font supports the characters

**Issue**: Words don't appear in game
- **Solution**: Verify JSON syntax and import statements

**Issue**: Article not showing
- **Solution**: Check that word format uses `{word, article}` structure

---

## Step 6: Commit & Push

```bash
# Create feature branch
git checkout -b feat/add-spanish-language

# Add files
git add app/words/es.json app/words/index.ts app/components/Settings.vue README.md

# Commit
git commit -m "feat: Add Spanish language support with 50 words"

# Push
git push origin feat/add-spanish-language
```

---

## Step 7: Create Pull Request

**PR Title**: `feat: Add Spanish language support`

**PR Description**:
```markdown
### Summary
Adds Spanish (Español) language support to the Word Memory Game with 50 words including articles.

### Changes
- Added `app/words/es.json` with 50 Spanish words and articles
- Updated `app/words/index.ts` to include Spanish word pool
- Updated Settings.vue dropdown to show "Español" option
- Updated README.md language list

### Testing
- [ ] Language appears in settings dropdown
- [ ] Words display correctly in all game states
- [ ] Articles render properly (el/la)
- [ ] Settings persist across sessions
- [ ] All existing tests pass

### Screenshots
(Add screenshots if visual changes are noticeable)

### Checklist
- [ ] Minimum 50 words
- [ ] UTF-8 encoded
- [ ] Consistent with existing language formats
- [ ] Documentation updated
```

---

## Language-Specific Considerations

### Languages with Articles (like German/Spanish/French)
```json
[
  {"word": "palabra", "article": "la"},
  {"word": "casa", "article": "la"}
]
```
- Must include `article` field
- Article displays before word in memorization phase

### Languages without Articles (like Russian/English/Swedish)
```json
["word1", "word2", "word3"]
```
- Simple string array
- No article rendering needed

### Right-to-Left Languages
Not currently supported. Would require additional CSS and layout changes.

---

## Tips for Success

1. **Start with a smaller list** (20 words) to test quickly, then expand to 50+
2. **Verify JSON syntax** using a validator before testing
3. **Test on mobile view** to check text wrapping
4. **Check font rendering** - some languages need specific fonts
5. **Ask native speakers** to review word list for accuracy

---

## Need Help?

Review existing language files:
- `app/words/en.json` - Simple strings
- `app/words/de.json` - Objects with articles  
- `app/words/ru.json` - Cyrillic strings
