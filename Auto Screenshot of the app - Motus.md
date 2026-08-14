# Auto Screenshot of the app - Motus

Yes. Since your `bun run dev` already has a **database seeding function**, the prompt should tell the agent to **inspect and reuse that existing seed mechanism**, rather than creating another demo-data system.

More importantly, I want the agent to first inspect your existing Playwright setup, routes, seed flow, and Electron launch configuration before changing anything.

Use this prompt:

```text
I want you to build a DEVELOPMENT-ONLY Microsoft Store screenshot generator for Motus.

IMPORTANT:
- Do not modify production application behavior.
- Do not add screenshot functionality to the Motus UI.
- Do not modify the existing master E2E test unless absolutely necessary.
- Do not create a second database seeding system.
- Do not create fake data manually if the existing development seed system can be reused.
- Reuse the existing Playwright + Electron infrastructure.
- The final goal is a small, reliable script that generates polished screenshots for the Microsoft Store.
- Optimize for simplicity. Do not over-engineer this.

==================================================
FIRST: INSPECT THE EXISTING PROJECT
==================================================

Before writing any code, inspect the relevant existing implementation.

I need you to find and understand:

1. Existing Playwright/Electron setup:
   - tests/e2e/master.spec.ts
   - playwright.config.* if present
   - package.json scripts
   - how Electron is launched
   - how the test window is obtained
   - existing helpers/fixtures

2. Existing development seed functionality:
   - Find exactly what runs when I execute:
       bun run dev
   - Find the existing database seed function/mechanism.
   - Determine how the development database is populated.
   - Determine whether the seed operation is idempotent/resettable.
   - Determine whether it can be reused for screenshot generation without modifying production data.

3. Existing routes/pages:
   - Dashboard
   - Members
   - Member Profile
   - Payments
   - Upcoming Dues
   - Dues
   - Reports
   - Any other visually strong pages

4. Existing authentication/onboarding flow:
   - Determine whether the seeded development DB already has a logged-in/session-ready state.
   - Do not invent another authentication mechanism.

5. Existing Electron configuration:
   - How the application resolves its database/user-data directory.
   - Whether the Playwright test already uses a temporary userDataDir.
   - How to create an isolated screenshot environment.

Do NOT start implementation until you understand these pieces.

==================================================
GOAL
==================================================

Create a dedicated Playwright test/script for generating Microsoft Store screenshots.

Suggested location:

    tests/e2e/store-screenshots.spec.ts

Use the existing Electron + Playwright infrastructure.

The script should produce a directory such as:

    store-screenshots/

with files such as:

    01-dashboard.png
    02-members.png
    03-member-profile.png
    04-payments.png
    05-upcoming-dues.png
    06-reports.png

The exact screens should be determined after inspecting the existing UI.

==================================================
USE EXISTING DEVELOPMENT SEED DATA
==================================================

This is important.

Motus already has a development database seed mechanism used by:

    bun run dev

DO NOT create another independent seed-data implementation.

Instead:

1. Find the existing seed functionality.
2. Determine whether it can be invoked before screenshot generation.
3. Reuse it to create/populate the screenshot environment.
4. If it needs a small extension to make the data suitable for screenshots, modify the existing seed mechanism rather than creating a duplicate.

The screenshot environment must contain realistic and visually useful data.

Avoid obvious test-looking data such as:

    Test User
    E2E Gold Plan
    Jane Smith
    test@example.com

unless those values are already part of the existing development seed and there is a strong reason to keep them.

Prefer realistic demo data if the existing seed system already provides it.

DO NOT fabricate a huge dataset simply for screenshots.

Use the minimum amount of data necessary to make each screen look populated and realistic.

==================================================
DATABASE ISOLATION
==================================================

The screenshot generator must NEVER operate on my normal production database.

Prefer the same isolated temporary userDataDir/database strategy already used by the existing Playwright tests.

For example, if the current test suite already does something similar to:

    const userDataDir = path.join(os.tmpdir(), ...);

reuse that pattern.

The screenshot process should be disposable:

    launch isolated Motus
        ↓
    initialize/seed development data
        ↓
    capture screenshots
        ↓
    close Motus
        ↓
    temporary data can be discarded

Do not touch the actual production database.

==================================================
SCREENSHOT SIZE
==================================================

Generate screenshots at a consistent desktop resolution suitable for Microsoft Store presentation.

Default:

    1920 × 1080

Use Playwright's screenshot functionality.

Do NOT capture the Windows desktop or taskbar.

The screenshot should contain the Motus application viewport/window content only.

If the existing Electron configuration has a suitable fixed window size, use it consistently.

Do not arbitrarily change production window behavior just for screenshots.

==================================================
SCREENSHOT SELECTION
==================================================

Do NOT automatically screenshot every route.

Select approximately 5–6 strong screenshots that communicate the product well.

Initial candidates:

1. Dashboard
2. Members
3. Member Profile
4. Payments → Transactions
5. Payments → Upcoming
6. Reports

But inspect the actual UI first.

If another page is significantly better visually or communicates Motus functionality better, use it instead.

The screenshots should collectively communicate:

- gym overview
- member management
- individual member management
- payments
- recurring/upcoming billing
- reporting/analytics

==================================================
SCREENSHOT QUALITY
==================================================

These are not automated-test screenshots.

They are marketing/store assets.

Therefore:

- Ensure the page has finished loading.
- Ensure data has finished rendering.
- Ensure tables are populated.
- Ensure no loading spinners are visible.
- Ensure dialogs are closed unless the dialog itself is the intended screenshot.
- Ensure no developer/debug UI is visible.
- Ensure no test labels are visible.
- Ensure the selected member/profile contains useful information.
- Ensure the page is scrolled to the most useful position.
- Ensure the screenshot composition is visually balanced.

Do not simply call:

    page.screenshot()

immediately after navigation.

Wait for meaningful UI elements.

For example:

    navigate
      ↓
    wait for page/table/heading
      ↓
    wait for data
      ↓
    optionally scroll to important section
      ↓
    screenshot

==================================================
MEMBER PROFILE SCREENSHOT
==================================================

For the Member Profile screenshot:

- Find a suitable seeded member.
- Open their profile through the normal UI.
- Choose a member with useful data.
- Prefer a member with:
  - active subscription
  - payment history
  - attendance/history
  - upcoming recurring dues
  - other useful profile information

Do not hardcode a database ID if there is a better way to locate the seeded member.

If the existing seed has a deterministic demo member, use that.

==================================================
PAYMENTS SCREENSHOTS
==================================================

Capture the Payments page in useful states.

Transactions screenshot:

    Payments
    [ Transactions ] [ Upcoming ]

with populated actual payment history.

Upcoming screenshot:

    Payments
    [ Transactions ] [ Upcoming ]

with populated scheduled recurring charges.

The Upcoming screenshot should demonstrate the recurring billing feature clearly.

Make sure the table has enough rows to look intentional but not so many that the UI becomes cluttered.

==================================================
DASHBOARD SCREENSHOT
==================================================

The dashboard should look like a real gym is operating.

Prefer seeded data that produces:

- useful member statistics
- payments/revenue
- attendance
- expenses
- activity/operations
- other dashboard widgets

Do not add special screenshot-only UI.

==================================================
REPORT SCREENSHOT
==================================================

If the Reports page is visually strong:

- navigate to it
- make sure it has meaningful data
- choose a useful report/time period
- capture it at 1920×1080

If it looks empty or weak with the existing seed data, choose another page instead.

==================================================
PLAYWRIGHT ARCHITECTURE
==================================================

Create a dedicated:

    tests/e2e/store-screenshots.spec.ts

Do not put Store screenshot logic inside:

    master.spec.ts

The existing functional E2E tests should remain responsible for functional testing.

Store screenshot tests should only be responsible for producing Store assets.

Reuse existing helpers where practical.

Do not duplicate Electron launching logic unnecessarily if the existing test infrastructure already exposes a reusable helper.

==================================================
SCREENSHOT HELPER
==================================================

A small helper is acceptable, for example:

    captureScreenshot(page, route, filename)

It should:

1. Navigate if necessary.
2. Wait for the expected page UI.
3. Ensure the UI is stable.
4. Capture the screenshot.

Do not create an elaborate screenshot framework.

==================================================
OUTPUT
==================================================

Save screenshots under:

    store-screenshots/

Ensure the directory is ignored by Git if appropriate.

Use deterministic names:

    01-dashboard.png
    02-members.png
    03-member-profile.png
    04-payments-transactions.png
    05-payments-upcoming.png
    06-reports.png

If the project already has an appropriate artifact/output directory, use that instead.

==================================================
IMPORTANT: DO NOT CHANGE PRODUCTION UI
==================================================

Do not make UI changes simply to make screenshots easier.

If a screen looks bad, report that instead of changing the production UI.

The purpose of this task is to capture the current V1 product.

If something genuinely prevents screenshot generation, fix only the test/screenshot infrastructure.

==================================================
TESTING
==================================================

After implementation, actually run the screenshot generator.

Do not just typecheck it.

Verify that:

1. Motus launches.
2. Existing development seed data is available.
3. Dashboard loads.
4. Members loads.
5. A suitable member can be opened.
6. Payments loads.
7. Upcoming loads with scheduled charges.
8. Reports loads if selected.
9. Screenshots are actually written to disk.
10. Screenshots are 1920×1080 or the chosen consistent dimensions.

Open/inspect the generated screenshots if possible to verify they are visually useful.

==================================================
PACKAGE SCRIPT
==================================================

Add a convenient package script, for example:

    "screenshots:store": "bunx playwright test tests/e2e/store-screenshots.spec.ts"

But first inspect the project's existing package scripts and follow its conventions.

The final command should be simple, ideally:

    bun run screenshots:store

==================================================
IMPORTANT DESIGN PRINCIPLE
==================================================

Do not over-engineer this.

I already have:

- Electron
- Playwright
- E2E tests
- development database
- development seed functionality

Use those.

Do not introduce:
- another browser automation library
- another database
- another seed system
- another authentication system
- another screenshot library
- complicated abstractions
- unnecessary production changes

Use Playwright's existing:

    page.screenshot()

==================================================
FINAL RESPONSE
==================================================

When finished, report:

1. What existing seed mechanism you found and how it was reused.
2. What screenshot test file was created/modified.
3. What package script was added.
4. Which screenshots were generated.
5. Their exact dimensions.
6. Where they were saved.
7. What demo data/state was used.
8. Tests/checks executed.
9. Any screens that could not be captured and why.

Most importantly:

Do not claim screenshot generation succeeded unless you actually ran it and confirmed the files were created.
```

### Why this prompt is better for your situation

The key difference is that you're telling the agent **"investigate first"** rather than assuming how your seed system or Playwright setup works.

Your existing setup already has an Electron E2E flow, and you said `bun run dev` seeds the DB. So the agent should ideally end up with:

```text
Existing seed
      ↓
isolated Playwright/Electron environment
      ↓
existing Motus UI
      ↓
page.screenshot()
      ↓
Microsoft Store screenshots
```

No new database tooling, no new fake-data generator, and no changes to the actual application.

**One additional recommendation:** have the agent generate the screenshots first, then you can show me the resulting screenshots. I can help you decide which 5–6 are actually strongest for the Microsoft Store and what, if anything, should be adjusted before submission.