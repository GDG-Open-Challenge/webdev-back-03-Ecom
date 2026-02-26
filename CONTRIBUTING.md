# 🤝 Contributing to Ecommerce API

Thank you for your interest in contributing! This project is a **debugging challenge** — your goal is to find and fix the 5 intentional bugs hidden in the ecommerce API codebase. Here's how to get started.

---

## 🚀 Getting Started

### 1. Fork the Repository

Click the **Fork** button in the top-right corner of the GitHub repo page. This creates your own copy of the project under your GitHub account.

### 2. Clone Your Fork

```bash
git clone https://github.com/<your-username>/webdev-back-03-Ecom.git
cd webdev-back-03-Ecom
```

### 3. Add the Upstream Remote

Keep your fork synced with the original repo:

```bash
git remote add upstream https://github.com/GDG-Open-Challenge/webdev-back-03-Ecom.git
git fetch upstream
```

---

## 🔀 Branching Strategy

Create a **separate branch** for each bug fix. Use this naming convention:

```
fix/issue-<number>-<short-description>
```

**Examples:**

```bash
git checkout -b fix/issue-1-no-stock-validation
git checkout -b fix/issue-2-create-order-errors
git checkout -b fix/issue-3-get-orders-returns-all-orders
git checkout -b fix/issue-4-product-update-missing-validation
git checkout -b fix/issue-5-delete-order-success-response
```

> [!IMPORTANT]
> Always branch off from the latest `main` branch:
> ```bash
> git checkout main
> git pull upstream main
> git checkout -b fix/issue-<number>-<description>
> ```

---

## 🛠 Making Changes

1. **Pick an issue** from the GitHub Issues tab
2. **Comment on the issue** to let others know you're working on it
3. **Read the full description** in `BUG_REPORT.md` — understand the bug behavior, root cause, and affected file
4. **Fix the bug** in the relevant file(s)
5. **Test your fix** by verifying the API endpoints work correctly with proper validation and behavior

### Testing Locally

**1. Install dependencies:**
```bash
npm install
```

**2. Set up environment variables:**
Create a `.env` file with:
```
MONGODB_URI=mongodb://localhost:27017/ecommerce
PORT=5000
NODE_ENV=development
```

**3. Start MongoDB:**
```bash
docker run -d -p 27017:27017 --name mongodb mongo
```

**4. Start the server:**
```bash
npm start
# or for development with auto-reload
npm run dev
```

The API will be available at `http://localhost:5000`

**5. Test the API using Postman or curl:**
```bash
# List all products
curl http://localhost:5000/api/products

# Create an order
curl -X POST http://localhost:5000/api/orders \
  -H "Content-Type: application/json" \
  -d '{
    "userId": "user123",
    "items": [{"productId": "{id}", "quantity": 1}],
    "shippingAddress": "123 Main St"
  }'
```

---

## 📝 Commit Message Convention

Follow the [Conventional Commits](https://www.conventionalcommits.org/) standard:

```
fix(scope): brief description of the fix (#issue-number)
```

**Examples:**

```bash
git commit -m "fix(order): add stock validation before creating order (#1)"
git commit -m "fix(order): add input validation and descriptive error messages (#2)"
git commit -m "fix(order): filter user orders by userId instead of returning all (#3)"
git commit -m "fix(product): add validation for required fields in update (#4)"
git commit -m "fix(order): return proper success response when deleting order (#5)"
```

> [!NOTE]
> Scope should be the affected area: `order`, `product`, `user`, `validation`, `error-handling`, etc.

---

## 🚀 Submitting a Pull Request

### 1. Push Your Branch

```bash
git push origin fix/issue-<number>-<description>
```

### 2. Open a Pull Request

- Go to your fork on GitHub
- Click **"Compare & pull request"**
- Set the base repository to `GDG-Open-Challenge/webdev-back-03-Ecom` and base branch to `main`
- **You MUST follow the PR format below** — PRs that do not follow this format will be requested to revise

---

## 📋 Required Pull Request Format

Every pull request **must** follow this exact structure. Copy this template and fill in every section. Incomplete submissions will not be reviewed.

```markdown
## 🐛 Bug Fix: Issue #<number> — <Issue Title>

### 📌 Summary

| Field             | Details                                      |
|-------------------|----------------------------------------------|
| **Issue**         | #<number>                                    |
| **Title**         | <Issue title>                                |
| **Severity**      | 🟢 Low / 🟡 Medium / 🔴 High               |
| **Category**      | Bug / UX / Accessibility / Performance       |
| **File(s)**       | `<file path>`                                |
| **Function(s)**   | `<function name(s)>`                         |

---

### 🔍 Bug Description

<Explain what the bug does in your own words. Describe the incorrect behavior
that a user would experience. Be specific — include what you observe vs. what
is expected.>

**Steps to Reproduce:**
1. <Step 1>
2. <Step 2>
3. <Step 3>

**Expected Behavior:** <What should happen>

**Actual Behavior:** <What currently happens>

---

### 🔬 Root Cause Analysis

<Explain WHY the bug occurs at a technical level. Identify the exact code pattern,
JavaScript behavior, CSS property, or logic error that causes the issue. This is
the most important section — demonstrate that you understand the underlying cause,
not just the symptom.>

---

### ✅ Fix Applied

<Explain your fix and WHY it resolves the root cause. Don't just say "I changed X
to Y" — explain the reasoning behind your approach and why it's the correct solution.>

---

### 📄 Code Changes

#### Before (Buggy Code)

```js
// File: <file path>
// Line(s): <line number(s)>
<paste the exact original buggy code here>
```

#### After (Fixed Code)

```js
// File: <file path>
// Line(s): <line number(s)>
<paste your corrected code here>
```

> If your fix spans multiple files, include a Before/After block for each file.

---

### 🧪 Testing & Verification

Describe how you tested your fix:

- [ ] Verified the fix resolves the described bug
- [ ] Confirmed no regressions — other API endpoints still work correctly
- [ ] Tested with valid inputs
- [ ] Tested with invalid/edge case inputs
- [ ] Verified error messages are helpful and descriptive
- [ ] Tested database persistence (data saves correctly)

**Test Evidence:**
<Describe what you tested and what the results were. Screenshots are encouraged
but not required.>

---

### 📚 Additional Notes (Optional)

<Any other context, related issues you noticed, alternative approaches considered,
or references to documentation/articles that helped you understand the bug.>
```

---

## ⚠️ PR Review Criteria

Your pull request will be evaluated on:

| Criterion | What We Look For |
|-----------|-----------------|
| **Correctness** | Does the fix actually resolve the bug without introducing new issues? |
| **Root Cause Understanding** | Did you identify WHY the bug occurs, not just WHAT to change? |
| **Code Quality** | Is the fix minimal, clean, and focused on the bug? |
| **Before/After Clarity** | Are the code changes clearly documented with exact line references? |
| **Testing** | Did you verify the fix works with valid and invalid inputs? |
| **Format Compliance** | Did you follow the required PR template completely? |

> [!CAUTION]
> PRs that skip sections, leave placeholders unfilled, or bundle multiple bug fixes will be sent back for revision.

---

## ✅ Contribution Rules

| Rule | Details |
|------|---------|
| **One bug per PR** | Don't bundle multiple fixes in a single pull request |
| **No additional frameworks** | Use only existing tech stack: Node.js/Express, MongoDB |
| **Don't reformat code** | Fix only the bug; don't restructure, rename, or beautify the surrounding code |
| **Don't add dependencies** | No new npm packages or external tools unless necessary |
| **Test before submitting** | Verify your fix works end-to-end with proper API testing |
| **Follow the PR template** | Every section must be filled out completely |

---


## 💬 Need Help?

- Comment on the issue you're working on so others know it's in progress
- Ask questions in the issue thread if you're stuck
- Review other contributors' PRs to learn different approaches

---

## 📜 Code of Conduct

- Be respectful and constructive in all interactions
- Help other contributors when possible
- Give credit where credit is due
- Have fun debugging! 🐛
