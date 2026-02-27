# Contributing to Ikigai

Thank you for taking the time to contribute. This is a small project with a clear purpose — helping people reflect on what makes their life meaningful. Every contribution should serve that purpose.

---

## Ways to Contribute

### 🌱 New Questions
The most valuable contribution. Good Ikigai questions are:

- **Indirect** — they don't signal which dimension they're testing
- **Evocative** — they invite genuine reflection, not quick answers
- **Scenario-based** — "what would you do if..." reveals more than "do you like..."
- **Culturally grounded** — rooted in psychological research, not pop-psychology

When submitting a new question, please include:
1. The question text
2. Which dimension it targets (L / G / P / W)
3. The four answer options with a brief note on why each maps to a different score level
4. A sentence on the psychological principle it draws from

### 🌍 Translations
The interface is currently in English. To add a language:

1. Fork the repository
2. Copy `index.html` → `index-[lang].html` (e.g. `index-ca.html` for Catalan)
3. Translate all user-facing strings — questions, answers, labels, results text
4. Test thoroughly, especially result synthesis text (it must read naturally)
5. Open a PR — note that translation quality is important; machine-translated PRs without human review will not be merged

Current translation wishlist: Catalan, Spanish, French, Japanese, German, Portuguese.

### ♿ Accessibility
Known areas for improvement:
- Full keyboard navigation through questions
- ARIA live regions for question transitions
- Screen reader testing on iOS VoiceOver and Android TalkBack
- Sufficient colour contrast in the fog/parchment tones

### 🐛 Bug Reports
Use the [bug report template](.github/ISSUE_TEMPLATE/bug_report.md). Please include:
- Browser and version
- Device type (desktop/mobile/tablet)
- Steps to reproduce
- What you expected vs. what happened

### 💡 Feature Ideas
Use the [feature request template](.github/ISSUE_TEMPLATE/feature_request.md). Please read existing issues first to avoid duplicates.

---

## Development Setup

No build tools needed. Edit `index.html` directly and open in a browser.

For live-reloading during development, a simple option:
```bash
# With Python (built-in)
python3 -m http.server 8000
# Then open http://localhost:8000

# With Node (if installed)
npx serve .
```

---

## Code Style

This is intentionally a single-file project. Please keep it that way.

- **HTML**: Semantic, well-commented
- **CSS**: All custom properties at the top in `:root`. Group styles logically with section comments
- **JavaScript**: Plain ES6+, no frameworks. Functions grouped by concern with comments. Avoid clever one-liners that obscure intent

---

## Commit Messages

Use clear, lowercase, present-tense messages:
```
add question about creative environments (area: L)
fix likert score normalisation edge case
improve mobile emoji grid layout
translate interface to Catalan
```

---

## Pull Request Process

1. Fork → branch (`feature/my-thing` or `fix/issue-42`) → PR against `main`
2. Describe what you changed and why
3. Test on at least two browsers and mobile
4. PRs that degrade the aesthetic quality without strong justification will be declined — please discuss design changes in an issue first

---

## Code of Conduct

This project follows the [Contributor Covenant](https://www.contributor-covenant.org/) v2.1. Be kind. Be constructive. Remember the spirit of the project.
