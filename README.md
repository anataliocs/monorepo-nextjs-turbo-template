# Next.js Monorepo Base Template

A batteries-included Monorepo base template focused on speed and consistency across multiple Next.js apps.
It uses PNPM and Turborepo for tooling and workflows.

---

## Front-end Tech Stack (with context)

- 📏 **TypeScript 5** — Type-safe code across apps and packages. Strict configs help catch errors early.
- ⚡️ **Next.js 13** — React framework with file-based routing, image optimization, and hybrid rendering.
- ⚛️ **React 18** — Concurrent features and modern hooks API.
- 🌬️ **Tailwind CSS 3** — Utility-first styling for a rapid, consistent UI.
- 📕 **Storybook 7** — Isolated UI development and documentation for components.
- 🧪 **Testing Library** — Tests from the user’s perspective; pairs with Jest.
- 🃏 **Jest** — Fast unit tests with a great ecosystem.
- 🎭 **Playwright** — Cross-browser E2E tests with auto-waiting and excellent tracing.
- 💡 **Lighthouse** — Performance, accessibility, SEO audits for web quality.
- 🧹 **ESLint** — Linting with shared rules for consistent code style and best practices.
- 🤖 **CommitLint** — Enforces Conventional Commit messages to produce clean history and semantic releases.
- 💖 **Prettier** — Opinionated formatter that keeps diffs minimal and readable.
- 📦 **pnpm** — Fast, disk-efficient package manager with workspaces.
- 🏎️ **Turborepo** — Task orchestration, caching, and parallelization across the monorepo.
- 👷 **GitHub Actions** — CI for tests, lint, type-check, and builds.

---

## Monorepo layout

Organizes multiple apps and enables sharing code and tooling.

**Apps:**

- `apps/academy` — Main Next.js app (user-facing)
- `apps/admin` — Admin Next.js app

There may also be shared packages (for UI, utils, configs, etc.) placed under `packages/`.

---

## Getting started

Prerequisites:

- Node 18+
- pnpm 8+

**Clone and install:**

```terminaloutput
git clone https://github.com/Developer-DAO/academy-turbo
cd academy-turbo
pnpm install
```

**Environment variables:**

- Create `.env` files per app as needed (`apps/academy/.env.local`)
- Check each app’s `README` for required variables

---

## Develop

**Run one app:**

```terminaloutput
# Academy app
pnpm dev --filter academy

# Admin app
pnpm dev --filter admin
```

**Run all apps in parallel:**

```terminaloutput
pnpm -w dev
```

**Storybook (all projects):**

```terminaloutput
pnpm storybook:dev
```

---

## Build

**Build one app:**

```terminaloutput
# Academy
pnpm build --filter academy

# Admin
pnpm build --filter admin
```

**Analyze bundles:**

```terminaloutput
ANALYZE=true pnpm build --filter academy
```

**Preview production builds:**

```terminaloutput
pnpm start
```

---

## Test & Lint

**Unit tests (Jest + Testing Library):**

```terminaloutput
pnpm test:unit
```

**E2E tests (Playwright):**

```terminaloutput
pnpm test:e2e
```

**Lint and type-check:**

```terminaloutput
pnpm lint
pnpm typecheck
```

**Lighthouse (via shared config if present):**

```terminaloutput
# Example; adjust to your setup
pnpm lighthouse
```

---

## Examples

**Component example** (`apps/academy/src/components/ConnectButton.tsx`):

```tsx
import localFont from "@next/font/local";
import { ConnectButton as RainbowkitConnectButton } from "@rainbow-me/rainbowkit";
import Image from "next/image";
import { Button } from "ui/components/ui/button";

const bttf = localFont({ src: "../../public/fonts/BTTF.ttf" });

export const ConnectButton = () => {
  return (
    <RainbowkitConnectButton.Custom>
      {({
        account,
        chain,
        openAccountModal,
        openChainModal,
        openConnectModal,
        authenticationStatus,
        mounted,
      }) => {
        const ready = mounted && authenticationStatus !== "loading";
        const connected =
          ready &&
          account &&
          chain &&
          (!authenticationStatus || authenticationStatus === "authenticated");

        if (!ready) {
          return (
            <Button className={`connect-button ${bttf.className}`} disabled>
              loading
            </Button>
          );
        }

        return (
          <>
            {connected !== true ? (
              <Button
                onClick={openConnectModal}
                className={`connect-button ${bttf.className} hover:bg-[var(--button-secondary-dark)]`}
              >
                Connect
              </Button>
            ) : chain?.unsupported === true ? (
              <Button
                onClick={openChainModal}
                className={`connect-button ${bttf.className} hover:bg-[var(--button-secondary-dark)]`}
              >
                Switch Network
              </Button>
            ) : (
              <button onClick={openAccountModal}>
                <Image
                  src={account?.ensAvatar !== undefined ? account.ensAvatar : "/avatar.png"}
                  alt="account avatar"
                  width={50}
                  height={50}
                  className="border-3 rounded-full border-black object-cover p-0 opacity-80 shadow-sm hover:h-14 hover:w-14 hover:border-amber-400"
                />
              </button>
            )}
          </>
        );
      }}
    </RainbowkitConnectButton.Custom>
  );
};
```

**Page example** (`apps/academy/src/pages/index.tsx`):

```tsx
import Image from "next/image";
import Link from "next/link";
import type { ReactElement } from "react";
import { Icons, LearnWeb3Banner, PartnerBanner } from "ui";
import PageSeoLayout from "@/components/PageSeoLayout";
import type { NextPageWithLayout } from "@/pages/_app";

const Home: NextPageWithLayout = () => {
  return (
    <div className="relative overflow-hidden bg-[url('/bg_home.png')] bg-cover bg-center bg-no-repeat">
      {/* content omitted for brevity */}
    </div>
  );
};

Home.getLayout = function getLayout(page: ReactElement) {
  return (
    <PageSeoLayout
      title="Learn Web3 With Friends"
      description="Start your journey to become a Web3 Developer today."
    >
      {page}
    </PageSeoLayout>
  );
};

export default Home;
```

---

## Add a new page (Next.js pages router)

This repo uses the pages/ directory in each app.

Example: create a simple page in Academy at `apps/academy/src/pages/hello.tsx`

```tsx
import type { NextPage } from "next";

const Hello: NextPage = () => {
  return <div className="p-6 text-xl">Hello from Academy 👋</div>;
};

export default Hello;
```

Start the dev server and navigate to: `/hello`

```terminaloutput
pnpm dev --filter academy
```

---

## Create or extend a shared package

Share components, utilities, or configs between apps:

**1) Create the package structure**

```terminaloutput
mkdir -p packages/ui/src
cd packages/ui
pnpm init -y
```

Add a basic `package.json` (example):

```json
{
  "name": "@acme/ui",
  "version": "0.1.0",
  "private": false,
  "main": "dist/index.js",
  "module": "dist/index.mjs",
  "types": "dist/index.d.ts",
  "scripts": {
    "build": "tsup src/index.tsx --dts",
    "dev": "tsup src/index.tsx --watch"
  }
}
```

**2) Add it to `pnpm-workspace.yaml`**

```yaml
packages:
  - apps/*
  - packages/*
```

**3) Export something from `packages/ui/src/index.tsx` and consume it from an app**

```tsx
// packages/ui/src/index.tsx
export const Hello = () => <div>Hello shared UI</div>;
```

```tsx
// apps/academy/src/pages/shared.tsx
import { Hello } from "@acme/ui";
export default function Shared() {
  return <Hello />;
}
```

**4) Build and run**

```terminaloutput
pnpm -w build
pnpm dev --filter academy
```

---

## Contributing

Please follow these guidelines:

- Use pnpm for all package operations.
- Create feature branches from main: feat/short-description or fix/issue-123.
- Commit messages follow Conventional Commits (enforced by CommitLint):
  - feat: add new user profile card
  - fix: correct null check in lesson loader
  - chore(ci): update node version in workflow
- Before pushing:
  - pnpm lint
  - pnpm test:unit
  - pnpm test:e2e (if affected)
  - pnpm build --filter <app>
- Open a pull request with a clear description, screenshots for UI changes, and mention affected apps/packages.

Code review checklist:

- Types are correct and strictness is maintained
- Tests updated/added
- No ESLint/Prettier issues

---

## CI/CD

GitHub Actions run lint, type-check, tests, and builds on pull requests and main merges.
Leverage Turborepo caching for faster pipelines.

---

## License

This project is open-source. See the LICENSE file in the repository root for details.

---

## Next steps

- Add a packages/ui shared library (buttons, banners) and consume it in both apps.
- Extract shared config packages (eslint-config, jest-config, tailwindcss-config, typescript-config) for consistency.
- Add more Playwright E2E coverage for critical flows.
- Set up Lighthouse CI to track performance and a11y over time.
- Publish shared packages to a private registry if needed (GitHub Packages or similar).

---
