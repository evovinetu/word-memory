# Task: Add Lithuanian Language Support to Word Memory Game

## Context
This is a forked open-source repository of the **Word Memory Game** - a Nuxt 4 web application that tests and trains word memory. The app currently supports three languages:
- **English (en)**: Simple string array format
- **Deutsch (de)**: Objects with `word` and `article` fields (German articles: der/die/das)
- **Русский (ru)**: Simple string array format (Cyrillic script)

## Quick Start

To add a new language to the Word Memory Game, follow this step-by-step guide.

### Prerequisites
- Node.js installed
- pnpm package manager
- Basic understanding of Vue 3 and TypeScript
- UTF-8 text editor for word list creation

---

## Objective
Extend the application to support **Lithuanian (lt)** language by implementing comprehensive language switching functionality throughout the codebase.

---

## Repository Architecture Analysis

### Project Structure
```
word-memory/
├── app/                          # Nuxt 3 application source
│   ├── components/               # Vue components
│   │   ├── Chart.vue            # Performance chart component
│   │   └── Settings.vue         # Game settings modal
│   ├── composables/              # Vue composables (state management)
│   │   ├── useGameSettings.ts   # Game state & settings
│   │   └── usePlayHistory.ts    # Play history tracking
│   ├── tests/                    # Playwright E2E tests
│   │   └── game.spec.ts         # Game flow tests
│   ├── words/                    # Language word pools
│   │   ├── en.json              # English words
│   │   ├── de.json              # German words (with articles)
│   │   ├── ru.json              # Russian words (Cyrillic)
│   │   └── index.ts             # Word pool exports
│   ├── app.vue                   # Main application component
│   └── words.json                # English word list backup
├── backend/                      # Optional API server
│   ├── src/
│   │   ├── db.ts                # Prisma database client
│   │   └── index.ts             # Express API server
│   └── prisma/
│       └── schema.prisma        # Database schema
├── scripts/                      # Word list generation scripts
│   ├── generate_wordlists.py
│   └── lang-German.py
├── nuxt.config.ts                # Nuxt configuration
├── playwright.config.ts          # Playwright test configuration
├── package.json                  # Dependencies & scripts
└── README.md                     # Project documentation
```

### Application Architecture

#### 1. **Language Data Structure**
- **Location**: `app/words/index.ts`
- **Type Definition**:
  ```typescript
  export type WordItem = string | { word: string; article?: string }
  ```
- **Word Pools Record**:
  ```typescript
  export const wordPools: Record<string, WordItem[]> = {
    en,  // Simple strings
    de,  // Objects with articles (der/die/das)
    ru   // Simple strings (Cyrillic)
  }
  ```

#### 2. **State Management**
- **Composable**: `useGameSettings()`
- **Stored Settings** (localStorage key: `'word_memory_settings'`):
  - `memorizeSeconds`: Timer for memorization phase (default: 5)
  - `delaySeconds`: Countdown before play phase (default: 3)
  - `playSeconds`: Active selection timer (default: 15)
  - `targetWordsCount`: Words to memorize (default: 4)
  - `totalGridWords`: Total words in grid (default: 12)
  - **`language`**: Current language code (default: `'en'`) ← NEW
  - `fontSize`: UI font size (small/medium/large/xlarge)

#### 3. **Game Flow**
```
start → memorize (show words with articles) → delay (countdown) → play (select words) → end (score display)
```

#### 4. **Word Rendering Logic** (`app.vue`)
```typescript
// Extract word text
function getWordText(item) {
  return typeof item === 'string' ? item : item.word
}

// Extract article (for German-style languages)
function getWordArticle(item) {
  if (!item || typeof item === 'string') return null
  return item.article || null
}
```

---

## Current Language Implementation Details

### English (`app/words/en.json`)
- **Format**: Simple string array
- **Count**: 754 words
- **Word Length**: 3-10 characters
- **Categories**: nouns
- **Example**:
  ```json
  ["air", "alarm", "anchor", "apple", ...]
  ```

### German (`app/words/de.json`)
- **Format**: Objects with `word` and `article`
- **Articles**: der (masculine), die (feminine), das (neuter)
- **Count**: 346 words
- **Word Length**: 3-13 characters
- **Categories**: nouns
- **Example**:
  ```json
  [
    {"word": "Apfel", "article": "der"},
    {"word": "Haus", "article": "das"},
    {"word": "Sonne", "article": "die"}
  ]
  ```

### Russian (`app/words/ru.json`)
- **Format**: Simple string array (Cyrillic)
- **Count**: 331 words
- **Word Length**: 1-15 characters
- **Categories**: nouns
- **Example**:
  ```json
  ["яблоко", "дом", "вода", "машина", ...]
  ```

---

## UI Components Analysis

### Settings Component (`app/components/Settings.vue`)
```vue
<!-- Language dropdown -->
<select id="language" v-model="language" class="setting-select">
  <option value="en">English</option>
  <option value="de">Deutsch</option>
  <option value="ru">Русский</option>
</select>
```

### Main App Component (`app/app.vue`)
- Uses `wordPools[language.value]` to select word pool
- Renders articles with `.word-article` CSS class (opacity: 0.55)
- Handles both string and object word formats transparently

---

## Testing Strategy

### Test Suite (`app/tests/game.spec.ts`)
**Test Cases**:
1. Initial start screen display
2. Full game lifecycle (start → memorize → delay → play → end)
3. Settings persistence in localStorage
4. Performance chart rendering
5. **Deutsch language support with articles**

**Run Tests**:
```bash
pnpm test  # Builds + Playwright tests + video organization
```

### Test Environment
- **Port**: 26090 (configured in `playwright.config.ts`)
- **Browser**: Chromium (headless)
- **Video Recording**: Enabled for all tests

---

## Contribution Requirements

### GitHub Workflows
**Deploy Workflow** (`.github/workflows/deploy.yml`):
```yaml
# Runs on push to master/main branches
# 1. Install dependencies
# 2. Install Playwright browsers
# 3. Run pnpm test (MUST PASS)
# 4. Deploy to GitHub Pages
```

### CI/CD Requirements
- ✅ All tests must pass before deployment
- ✅ Build must complete successfully
- ✅ No console errors

---

## Proposed Implementation: Lithuanian Language

### Lithuanian Language Characteristics
| Property | Value |
|----------|-------|
| **ISO Code** | `lt` |
| **Native Name** | Lietuvių kalba |
| **Script Type** | Latin (with diacritics) |
| **Articles** | No definite/indefinite articles |
| **Gender System** | Masculine/Feminine (noun classes) |
| **Special Characters** | Ą, Č, Ę, Ė, Į, Š, Ų, Ū, Ž |

### Implementation Approach

#### Step 1: Create Lithuanian Word List
- **File**: `app/words/lt.json`
- **Format**: Simple string sorted array (no articles in Lithuanian)
- **Word Count Target**: 500 common Lithuanian nouns (more is better)
- **Word Length Target**: 1-10 characters (shorter is better)
- **Encoding**: UTF-8 with diacritic characters

#### Step 2: Update Word Pools Index
- **File**: `app/words/index.ts`
- **Changes**:
  ```typescript
  import lt from './lt.json'  // Add import
  
  export const wordPools = {
    en, de, ru, lt  // Add lt to record
  }
  ```

#### Step 3: Update Settings UI
- **File**: `app/components/Settings.vue`
- **Changes**:
  ```vue
  <select id="language" v-model="language">
    <option value="en">English</option>
    <option value="de">Deutsch</option>
    <option value="ru">Русский</option>
    <option value="lt">Lietuvių</option>  <!-- Add -->
  </select>
  ```

#### Step 4: Update Documentation
- **Files**: `README.md`
- **Changes**: Add Lithuanian to supported languages list

---

## Exact Files Expected to Change

| Priority | File Path | Action | Reason |
|----------|-----------|--------|--------|
| **1** | `app/words/lt.json` | **CREATE** | Lithuanian word list |
| **2** | `app/words/index.ts` | **MODIFY** | Import and export Lithuanian word pool |
| **3** | `app/components/Settings.vue` | **MODIFY** | Add "Lietuvių" option to language dropdown |
| **4** | `README.md` | **MODIFY** | Update supported languages list |

### Optional/Testing Files (No Changes Required Unless Issues Found)
- `playwright.config.ts` - Test configuration
- `app/tests/game.spec.ts` - E2E tests (may need updates if language-specific assertions)

---

## Risks and Unknowns

### Low Risk
1. **UTF-8 Encoding**: Modern browsers handle UTF-8 well; Lithuanian diacritics are standard Unicode
2. **Type Safety**: TypeScript types already support both string and object word formats
3. **Backward Compatibility**: Adding new language doesn't affect existing functionality

### Medium Risk
1. **Font Rendering**: Verify selected fonts support Lithuanian diacritics (Ą, Č, Ę, etc.)
   - **Mitigation**: Use system fonts or web fonts with broad Unicode coverage
2. **Text Length**: Lithuanian words may be longer than English; verify layout fits
   - **Mitigation**: Choose words up to 10 characters (shorter is better), test with various font sizes in Settings

### Unknowns (Need Verification)
1. **Article Rendering**: Since Lithuanian has no articles, `.word-article` elements won't render (expected behavior)
2. **Mobile Layout**: Verify word wrapping on small screens with longer words
3. **Playwright Tests**: May need to verify language selection test covers all 4+ languages

---

## Step-by-Step Development Plan

### Phase 1: Preparation (No Code Changes)
- [ ] Verify repository structure matches analysis
- [ ] Confirm Lithuanian character set requirements
- [ ] Compile Lithuanian sorted list of unique 500 common short nouns
- [ ] Test UTF-8 encoding with diacritics
- [ ] Review existing tests for language-specific code

### Phase 2: Implementation
#### Step 2.1: Create Word List
- [ ] Create `app/words/lt.json` with the compiled Lithuanian words
- [ ] Use simple string format (no articles)
- [ ] Include diacritic characters: Ą, Č, Ę, Ė, Į, Š, Ų, Ū, Ž
- [ ] Validate JSON syntax

#### Step 2.2: Update Word Pools
- [ ] Add `import lt from './lt.json'` to `app/words/index.ts`
- [ ] Add `lt` to `wordPools` record
- [ ] Verify TypeScript compiles without errors

#### Step 2.3: Update Settings UI
- [ ] Add `<option value="lt">Lietuvių</option>` to Settings dropdown
- [ ] Place in consistent order (alphabetical or by addition date)
- [ ] Test UI renders correctly

#### Step 2.4: Verify Build
- [ ] Run `pnpm build` without errors
- [ ] Check for TypeScript warnings
- [ ] Verify no console errors

### Phase 3: Testing
#### Step 3.1: Manual Testing
- [ ] Start dev server (`pnpm run dev`)
- [ ] Open Settings and verify "Lietuvių" option appears
- [ ] Select Lithuanian language
- [ ] Play game and verify words display correctly
- [ ] Test all game states (memorize, delay, play, end)
- [ ] Verify settings persist after page reload

#### Step 3.2: Automated Testing
- [ ] Run `pnpm test`
- [ ] Verify Playwright tests pass
- [ ] Check video recordings show Lithuanian text correctly
- [ ] Confirm no new test failures introduced

### Phase 4: Documentation & Finalization
- [ ] Update README.md with Lithuanian language
- [ ] Test on multiple browsers (Chrome, Firefox, Safari)
- [ ] Verify mobile responsiveness
- [ ] Review all changes for consistency

---

## Expected Changes Summary

### New Files Created
1. `app/words/lt.json` - Lithuanian word list

### Modified Files
1. `app/words/index.ts`:
   ```diff
   +import lt from './lt.json'
    
    export const wordPools: Record<string, WordItem[]> = {
      en,
      de,
      ru,
   +  lt
    }
   ```

2. `app/components/Settings.vue`:
   ```diff
     <select id="language" v-model="language">
       <option value="en">English</option>
       <option value="de">Deutsch</option>
       <option value="ru">Русский</option>
   +    <option value="lt">Lietuvių</option>
     </select>
   ```

3. `README.md`:
   ```diff
   + ## 🌐 Supported Languages
   + 
   + - **English (en)**: Simple English words
   + - **Deutsch (de)**: German words with articles (der/die/das)
   + - **Русский (ru)**: Russian Cyrillic words
   + - **Lietuvių (lt)**: Common Lithuanian words
   
   ```

---

## Testing Checklist

- [ ] Lithuanian word list compiles without errors
- [ ] JSON is valid and UTF-8 encoded
- [ ] Language appears in Settings dropdown
- [ ] Words display correctly during memorization phase
- [ ] Words display correctly in play grid
- [ ] No article rendering (expected for Lithuanian)
- [ ] Settings persist across sessions
- [ ] All Playwright tests pass
- [ ] Build completes successfully
- [ ] Manual gameplay works end-to-end

---

## Implementation Checklist

### Phase 1: Preparation (No Code Changes)
- [ ] Verify repository structure matches analysis
- [ ] Confirm Lithuanian character set requirements
- [ ] Compile Lithuanian sorted list of unique 500 common short nouns
- [ ] Test UTF-8 encoding with diacritics
- [ ] Review existing tests for language-specific code

### Phase 2: Implementation
#### Step 2.1: Create Word List
- [ ] Create `app/words/lt.json` the compiled Lithuanian words
- [ ] Use simple string format (no articles)
- [ ] Include diacritic characters: Ą, Č, Ę, Ė, Į, Š, Ų, Ū, Ž
- [ ] Validate JSON syntax

#### Step 2.2: Update Word Pools
- [ ] Add `import lt from './lt.json'` to `app/words/index.ts`
- [ ] Add `lt` to `wordPools` record
- [ ] Verify TypeScript compiles without errors

#### Step 2.3: Update Settings UI
- [ ] Add `<option value="lt">Lietuvių</option>` to Settings dropdown
- [ ] Place in consistent order (alphabetical or by addition date)
- [ ] Test UI renders correctly

#### Step 2.4: Verify Build
- [ ] Run `pnpm build` without errors
- [ ] Check for TypeScript warnings
- [ ] Verify no console errors

### Phase 3: Testing
#### Step 3.1: Manual Testing
- [ ] Start dev server (`pnpm run dev`)
- [ ] Open Settings and verify "Lietuvių" option appears
- [ ] Select Lithuanian language
- [ ] Play game and verify words display correctly
- [ ] Test all game states (memorize, delay, play, end)
- [ ] Verify settings persist after page reload

#### Step 3.2: Automated Testing
- [ ] Run `pnpm test`
- [ ] Verify Playwright tests pass
- [ ] Check video recordings show Lithuanian text correctly
- [ ] Confirm no new test failures introduced

### Phase 4: Documentation & Finalization
- [ ] Update README.md with Lithuanian language
- [ ] Test on multiple browsers (Chrome, Firefox, Safari)
- [ ] Verify mobile responsiveness
- [ ] Review all changes for consistency

## Common Issues & Solutions

**Issue**: Special characters display as squares/boxes
- **Solution**: Ensure file is UTF-8 encoded and font supports the characters

**Issue**: Words don't appear in game
- **Solution**: Verify JSON syntax and import statements

**Issue**: Article not showing (for article-based languages)
- **Solution**: Check that word format uses `{word, article}` structure

---

## Notes

1. **No Backend Changes Required**: Language selection is client-side only
2. **No Database Changes**: Word pools are static JSON files
3. **No Migration Needed**: Adding language doesn't affect existing data
4. **Test Coverage**: Existing tests should cover new language automatically (uses same game logic)
