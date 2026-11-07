# Interior Studio — Tailwind CSS Source Code

Next.js App Router + Tailwind CSS v4. Design tokens live in `app/globals.css` (`@theme inline`), and every page/component uses Tailwind utility classes built on those tokens. Images referenced live in `public/images/`.

## File structure
```
app/about/page.tsx
app/contact/actions.ts
app/contact/page.tsx
app/globals.css
app/layout.tsx
app/page.tsx
app/portfolio/[slug]/page.tsx
app/portfolio/page.tsx
app/services/page.tsx
components/contact-form.tsx
components/cta-band.tsx
components/home/hero.tsx
components/home/sections.tsx
components/logo.tsx
components/ornament.tsx
components/page-hero.tsx
components/portfolio-grid.tsx
components/project-card.tsx
components/site-footer.tsx
components/site-header.tsx
lib/projects.ts
lib/services.ts
lib/utils.ts
```

## `app/about/page.tsx`

```tsx
import type { Metadata } from 'next'
import Image from 'next/image'
import { PageHero } from '@/components/page-hero'
import { Ornament, SectionHeading } from '@/components/ornament'
import { CtaBand } from '@/components/cta-band'

export const metadata: Metadata = {
  title: 'About',
  description: 'Meet Maison Ardenne — an interior design studio founded by Hélène Ardenne in Paris in 2009.',
}

const values = [
  { title: 'Craft', text: 'We work with joiners, upholsterers, stone masons and weavers whose skill is passed down through generations.' },
  { title: 'Patina', text: 'We choose materials that age with grace — brass, oak, linen, marble — so homes grow richer over time.' },
  { title: 'Restraint', text: 'Luxury, to us, is quiet. Every object earns its place; nothing is there merely to impress.' },
]

const team = [
  { name: 'Hélène Ardenne', role: 'Founder & Creative Director' },
  { name: 'Thomas Wren', role: 'Director, London Studio' },
  { name: 'Camille Duret', role: 'Senior Designer' },
  { name: 'Oliver Haig', role: 'Head of Procurement' },
]

export default function AboutPage() {
  return (
    <>
      <PageHero
        eyebrow="About the Studio"
        title={
          <>
            Fifteen years of <em className="text-primary">quiet</em> luxury
          </>
        }
        intro="Maison Ardenne was founded on a simple belief: that the most beautiful homes are those that feel lived in, loved and entirely personal."
      />

      <section className="mx-auto grid max-w-7xl items-center gap-16 px-6 py-20 md:py-28 lg:grid-cols-12 lg:px-10">
        <div className="relative isolate lg:col-span-5">
          <div className="relative aspect-[4/5] overflow-hidden rounded-t-[999px]">
            <Image
              src="/images/founder.png"
              alt="Hélène Ardenne, founder, standing in the studio among fabric samples"
              fill
              sizes="(min-width: 1024px) 40vw, 100vw"
              className="object-cover"
            />
          </div>
          <div aria-hidden="true" className="absolute -inset-3 -z-10 rounded-t-[999px] border border-accent/50" />
        </div>
        <div className="lg:col-span-6 lg:col-start-7">
          <p className="eyebrow text-accent">Our Founder</p>
          <h2 className="mt-4 text-4xl md:text-5xl">Hélène Ardenne</h2>
          <Ornament className="mt-6" />
          <div className="mt-8 space-y-5 leading-relaxed text-muted-foreground md:text-lg">
            <p>
              Raised between a Burgundy farmhouse and her grandmother’s Left Bank apartment, Hélène learned early that a
              home holds memory in its fabrics, its furniture and its light.
            </p>
            <p>
              After a decade at leading ateliers in Paris and London, she opened Maison Ardenne in 2009. Today the studio
              of eighteen designers works across Europe, yet every project still crosses her desk.
            </p>
          </div>
          <p className="mt-10 font-serif text-2xl italic text-primary">
            {'“'}I design rooms I would want to grow old in.{'”'}
          </p>
        </div>
      </section>

      <section className="bg-primary text-primary-foreground">
        <div className="mx-auto max-w-7xl px-6 py-24 md:py-32 lg:px-10">
          <div className="max-w-2xl">
            <p className="eyebrow text-accent">What We Value</p>
            <h2 className="mt-4 text-4xl md:text-5xl">Principles that guide every room</h2>
          </div>
          <ul className="mt-16 grid gap-12 md:grid-cols-3">
            {values.map((v, i) => (
              <li key={v.title} className="border-t border-primary-foreground/25 pt-8">
                <span className="font-serif text-xl italic text-accent">0{i + 1}</span>
                <h3 className="mt-4 text-3xl">{v.title}</h3>
                <p className="mt-4 leading-relaxed text-primary-foreground/80">{v.text}</p>
              </li>
            ))}
          </ul>
        </div>
      </section>

      <section className="mx-auto max-w-7xl px-6 py-24 md:py-32 lg:px-10">
        <SectionHeading align="center" eyebrow="The Studio" title="A small team, deeply involved" />
        <ul className="mt-16 grid grid-cols-2 gap-8 lg:grid-cols-4">
          {team.map((m) => (
            <li key={m.name} className="border-t border-border pt-6 text-center">
              <div
                aria-hidden="true"
                className="mx-auto flex size-20 items-center justify-center rounded-full bg-muted font-serif text-2xl italic text-primary"
              >
                {m.name
                  .split(' ')
                  .map((n) => n[0])
                  .join('')}
              </div>
              <h3 className="mt-5 text-2xl">{m.name}</h3>
              <p className="mt-1 text-sm text-muted-foreground">{m.role}</p>
            </li>
          ))}
        </ul>
      </section>

      <CtaBand />
    </>
  )
}
```

## `app/contact/actions.ts`

```ts
'use server'

export type ContactState = {
  status: 'idle' | 'success' | 'error'
  message?: string
  errors?: Partial<Record<'name' | 'email' | 'message', string>>
}

const EMAIL = /^[^\s@]+@[^\s@]+\.[^\s@]+$/

export async function submitEnquiry(_prev: ContactState, formData: FormData): Promise<ContactState> {
  const name = String(formData.get('name') ?? '').trim().slice(0, 120)
  const email = String(formData.get('email') ?? '').trim().slice(0, 200)
  const message = String(formData.get('message') ?? '').trim().slice(0, 4000)

  const errors: ContactState['errors'] = {}
  if (name.length < 2) errors.name = 'Please share your name.'
  if (!EMAIL.test(email)) errors.email = 'Please enter a valid email address.'
  if (message.length < 10) errors.message = 'Tell us a little about your project.'

  if (Object.keys(errors).length > 0) {
    return { status: 'error', errors, message: 'Please review the highlighted fields.' }
  }

  // Connect an email provider (e.g. Resend) here to deliver enquiries to the studio inbox.
  return {
    status: 'success',
    message: `Thank you, ${name.split(' ')[0]}. We’ll be in touch within two working days.`,
  }
}
```

## `app/contact/page.tsx`

```tsx
import type { Metadata } from 'next'
import Image from 'next/image'
import { ContactForm } from '@/components/contact-form'
import { Ornament } from '@/components/ornament'

export const metadata: Metadata = {
  title: 'Contact',
  description: 'Book a consultation with Maison Ardenne. Studios in Paris and London.',
}

const studios = [
  { city: 'Paris', address: ['14 Rue Jacob', '75006 Paris, France'], phone: '+33 1 42 00 00 00' },
  { city: 'London', address: ['22 Pimlico Road', 'London SW1W 8LJ, UK'], phone: '+44 20 7000 0000' },
]

export default function ContactPage() {
  return (
    <>
      <section className="mx-auto grid max-w-7xl gap-16 px-6 py-20 md:py-28 lg:grid-cols-12 lg:px-10">
        <div className="lg:col-span-5">
          <p className="eyebrow text-accent">Contact</p>
          <h1 className="mt-6 text-5xl leading-[1.02] md:text-7xl">
            Let&apos;s begin a <em className="text-primary">conversation</em>
          </h1>
          <Ornament className="mt-8" />
          <p className="mt-8 max-w-md text-pretty text-lg leading-relaxed text-muted-foreground">
            Share a little about your home and we will arrange an initial consultation, in person or by video.
          </p>
          <div className="relative mt-12 hidden aspect-[4/3] overflow-hidden lg:block">
            <Image
              src="/images/project-bedroom.png"
              alt="A serene bedroom with a linen headboard and arched window"
              fill
              sizes="40vw"
              className="object-cover"
            />
          </div>
        </div>
        <div className="lg:col-span-6 lg:col-start-7">
          <div className="bg-card p-8 shadow-sm ring-1 ring-border md:p-12">
            <h2 className="text-3xl">Project enquiry</h2>
            <p className="mt-2 text-sm text-muted-foreground">We respond to every enquiry within two working days.</p>
            <div className="mt-10">
              <ContactForm />
            </div>
          </div>
        </div>
      </section>

      <section className="border-t border-border bg-muted/60">
        <div className="mx-auto grid max-w-7xl gap-12 px-6 py-20 md:grid-cols-3 lg:px-10">
          {studios.map((s) => (
            <div key={s.city}>
              <h2 className="text-4xl">{s.city}</h2>
              <address className="mt-4 not-italic leading-relaxed text-muted-foreground">
                {s.address.map((l) => (
                  <span key={l} className="block">
                    {l}
                  </span>
                ))}
                <a href={`tel:${s.phone.replace(/\s/g, '')}`} className="mt-2 block text-foreground hover:text-primary">
                  {s.phone}
                </a>
              </address>
            </div>
          ))}
          <div>
            <h2 className="text-4xl">Hours</h2>
            <p className="mt-4 leading-relaxed text-muted-foreground">
              Monday to Friday, 9:30 – 18:00
              <br />
              Studio visits by appointment
            </p>
            <a href="mailto:studio@maisonardenne.com" className="mt-2 block hover:text-primary">
              studio@maisonardenne.com
            </a>
          </div>
        </div>
      </section>
    </>
  )
}
```

## `app/globals.css`

```css
@import 'tailwindcss';
@import 'tw-animate-css';
@import 'shadcn/tailwind.css';

@custom-variant dark (&:is(.dark *));

@theme inline {
  --font-serif: var(--font-cormorant), 'Cormorant Garamond', Georgia, serif;
  --font-sans: var(--font-jost), 'Jost', ui-sans-serif, system-ui, sans-serif;

  --color-ring: var(--ring);
  --color-input: var(--input);
  --color-border: var(--border);
  --color-destructive: var(--destructive);
  --color-accent-foreground: var(--accent-foreground);
  --color-accent: var(--accent);
  --color-muted-foreground: var(--muted-foreground);
  --color-muted: var(--muted);
  --color-secondary-foreground: var(--secondary-foreground);
  --color-secondary: var(--secondary);
  --color-primary-foreground: var(--primary-foreground);
  --color-primary: var(--primary);
  --color-popover-foreground: var(--popover-foreground);
  --color-popover: var(--popover);
  --color-card-foreground: var(--card-foreground);
  --color-card: var(--card);
  --color-foreground: var(--foreground);
  --color-background: var(--background);
  --radius-sm: calc(var(--radius) * 0.6);
  --radius-md: calc(var(--radius) * 0.8);
  --radius-lg: var(--radius);
  --radius-xl: calc(var(--radius) * 1.4);
}

:root {
  color-scheme: light;
  /* Warm neutral foundation */
  --background: #f6f1e9;
  --foreground: #2b2420;
  --card: #fbf8f3;
  --card-foreground: #2b2420;
  --popover: #fbf8f3;
  --popover-foreground: #2b2420;
  --muted: #ece4d7;
  --muted-foreground: #6b5f55;
  --border: #dcd0bf;
  --input: #dcd0bf;
  --ring: #6b2b2b;
  /* Rich accents: claret, forest, brass */
  --primary: #6b2b2b;
  --primary-foreground: #fbf8f3;
  --secondary: #2f4a3d;
  --secondary-foreground: #f6f1e9;
  --accent: #a9844f;
  --accent-foreground: #2b2420;
  --destructive: #a33a2a;
  --radius: 0.25rem;
}

@layer base {
  * {
    @apply border-border outline-ring/50;
  }
  html {
    scroll-behavior: smooth;
  }
  body {
    @apply bg-background text-foreground font-sans;
    font-feature-settings: 'kern', 'liga';
  }
  h1,
  h2,
  h3,
  h4 {
    @apply font-serif text-balance;
    font-weight: 400;
    letter-spacing: -0.01em;
  }
  ::selection {
    background: var(--primary);
    color: var(--primary-foreground);
  }
}

@utility eyebrow {
  font-family: var(--font-sans);
  font-size: 0.72rem;
  letter-spacing: 0.28em;
  text-transform: uppercase;
  font-weight: 500;
}
```

## `app/layout.tsx`

```tsx
import { Analytics } from '@vercel/analytics/next'
import type { Metadata, Viewport } from 'next'
import { Cormorant_Garamond, Jost } from 'next/font/google'
import { SiteHeader } from '@/components/site-header'
import { SiteFooter } from '@/components/site-footer'
import './globals.css'

const cormorant = Cormorant_Garamond({
  subsets: ['latin'],
  weight: ['300', '400', '500', '600'],
  style: ['normal', 'italic'],
  variable: '--font-cormorant',
})

const jost = Jost({
  subsets: ['latin'],
  weight: ['300', '400', '500'],
  variable: '--font-jost',
})

export const metadata: Metadata = {
  title: {
    default: 'Maison Ardenne — Interior Design Studio',
    template: '%s — Maison Ardenne',
  },
  description:
    'Maison Ardenne is an interior design studio creating timeless, quietly luxurious homes with warmth, craft, and a sense of history.',
  generator: 'v0.app',
}

export const viewport: Viewport = {
  colorScheme: 'light',
  themeColor: '#f6f1e9',
}

export default function RootLayout({
  children,
}: Readonly<{
  children: React.ReactNode
}>) {
  return (
    <html lang="en" className={`${cormorant.variable} ${jost.variable} bg-background`}>
      <body className="antialiased">
        <a
          href="#main"
          className="sr-only focus:not-sr-only focus:fixed focus:left-4 focus:top-4 focus:z-[60] focus:bg-primary focus:px-4 focus:py-2 focus:text-primary-foreground"
        >
          Skip to content
        </a>
        <SiteHeader />
        <main id="main">{children}</main>
        <SiteFooter />
        {process.env.NODE_ENV === 'production' && <Analytics />}
      </body>
    </html>
  )
}
```

## `app/page.tsx`

```tsx
import { HomeHero } from '@/components/home/hero'
import { FeaturedProjects, Philosophy, Press, ServicesPreview } from '@/components/home/sections'
import { CtaBand } from '@/components/cta-band'

export default function HomePage() {
  return (
    <>
      <HomeHero />
      <Philosophy />
      <FeaturedProjects />
      <ServicesPreview />
      <Press />
      <CtaBand />
    </>
  )
}
```

## `app/portfolio/[slug]/page.tsx`

```tsx
import type { Metadata } from 'next'
import Image from 'next/image'
import Link from 'next/link'
import { notFound } from 'next/navigation'
import { ArrowLeft, ArrowRight } from 'lucide-react'
import { getProject, projects } from '@/lib/projects'
import { Ornament } from '@/components/ornament'
import { CtaBand } from '@/components/cta-band'

export function generateStaticParams() {
  return projects.map((p) => ({ slug: p.slug }))
}

export async function generateMetadata({ params }: PageProps<'/portfolio/[slug]'>): Promise<Metadata> {
  const { slug } = await params
  const project = getProject(slug)
  if (!project) return {}
  return { title: project.title, description: project.summary }
}

export default async function ProjectPage({ params }: PageProps<'/portfolio/[slug]'>) {
  const { slug } = await params
  const project = getProject(slug)
  if (!project) notFound()

  const index = projects.findIndex((p) => p.slug === slug)
  const next = projects[(index + 1) % projects.length]
  const [, galleryA, galleryB] = project.gallery

  return (
    <article>
      <header className="mx-auto max-w-7xl px-6 pt-12 lg:px-10">
        <nav aria-label="Breadcrumb">
          <Link href="/portfolio" className="eyebrow inline-flex items-center gap-2 text-muted-foreground hover:text-primary">
            <ArrowLeft className="size-4" aria-hidden="true" /> Portfolio
          </Link>
        </nav>
        <div className="mt-10 grid gap-8 lg:grid-cols-12 lg:items-end">
          <div className="lg:col-span-8">
            <p className="eyebrow text-accent">
              {project.category} · {project.location}
            </p>
            <h1 className="mt-5 text-5xl leading-[1.02] md:text-7xl">{project.title}</h1>
          </div>
          <p className="text-pretty text-lg leading-relaxed text-muted-foreground lg:col-span-4">{project.summary}</p>
        </div>
      </header>

      <div className="relative mx-auto mt-12 aspect-[4/3] max-w-[1600px] md:aspect-[21/9]">
        <Image src={project.image} alt={`${project.title} main view`} fill priority sizes="100vw" className="object-cover" />
      </div>

      <section className="mx-auto grid max-w-7xl gap-16 px-6 py-20 md:py-28 lg:grid-cols-12 lg:px-10">
        <aside className="lg:col-span-4">
          <dl className="divide-y divide-border border-y border-border">
            {[{ label: 'Year', value: project.year }, ...project.details].map((d) => (
              <div key={d.label} className="flex justify-between gap-4 py-4">
                <dt className="eyebrow text-muted-foreground">{d.label}</dt>
                <dd className="text-right">{d.value}</dd>
              </div>
            ))}
          </dl>
          <div className="mt-10">
            <h2 className="eyebrow text-accent">Palette</h2>
            <ul className="mt-5 grid grid-cols-2 gap-4">
              {project.palette.map((c) => (
                <li key={c.name} className="flex items-center gap-3">
                  <span
                    aria-hidden="true"
                    className="size-10 shrink-0 rounded-full border border-border"
                    style={{ backgroundColor: c.hex }}
                  />
                  <span className="text-sm leading-tight">
                    {c.name}
                    <span className="block text-xs text-muted-foreground">{c.hex}</span>
                  </span>
                </li>
              ))}
            </ul>
          </div>
        </aside>

        <div className="space-y-14 lg:col-span-7 lg:col-start-6">
          <div>
            <h2 className="eyebrow text-accent">The Brief</h2>
            <p className="mt-5 font-serif text-3xl leading-snug md:text-4xl">{project.brief}</p>
          </div>
          <div>
            <h2 className="eyebrow text-accent">Our Approach</h2>
            <p className="mt-5 text-lg leading-relaxed text-muted-foreground">{project.approach}</p>
          </div>
        </div>
      </section>

      <section aria-label="Gallery" className="mx-auto grid max-w-7xl gap-6 px-6 lg:grid-cols-12 lg:px-10">
        <div className="relative aspect-[4/5] lg:col-span-5">
          <Image src={galleryA} alt={`${project.title} detail`} fill sizes="(min-width: 1024px) 40vw, 100vw" className="object-cover" />
        </div>
        <div className="relative aspect-[4/3] lg:col-span-7 lg:mt-24 lg:aspect-auto">
          <Image src={galleryB} alt={`${project.title} room view`} fill sizes="(min-width: 1024px) 58vw, 100vw" className="object-cover" />
        </div>
      </section>

      {project.quote && (
        <section className="mx-auto max-w-4xl px-6 py-24 text-center md:py-32">
          <Ornament className="justify-center" />
          <blockquote className="mt-10">
            <p className="font-serif text-3xl italic leading-snug md:text-5xl">
              {'“'}
              {project.quote.text}
              {'”'}
            </p>
            <footer className="eyebrow mt-8 text-muted-foreground">{project.quote.author}</footer>
          </blockquote>
        </section>
      )}

      <nav aria-label="Next project" className={project.quote ? '' : 'mt-24'}>
        <Link href={`/portfolio/${next.slug}`} className="group relative block overflow-hidden">
          <div className="relative aspect-[16/9] md:aspect-[21/7]">
            <Image
              src={next.image}
              alt=""
              fill
              sizes="100vw"
              className="object-cover transition-transform duration-[1200ms] group-hover:scale-105"
            />
            <div className="absolute inset-0 bg-foreground/55" />
          </div>
          <div className="absolute inset-0 flex flex-col items-center justify-center px-6 text-center text-background">
            <span className="eyebrow text-accent">Next Project</span>
            <span className="mt-4 inline-flex items-center gap-4 font-serif text-4xl md:text-6xl">
              {next.title} <ArrowRight className="size-8 transition-transform group-hover:translate-x-2" aria-hidden="true" />
            </span>
          </div>
        </Link>
      </nav>

      <CtaBand />
    </article>
  )
}
```

## `app/portfolio/page.tsx`

```tsx
import type { Metadata } from 'next'
import { PageHero } from '@/components/page-hero'
import { PortfolioGrid } from '@/components/portfolio-grid'
import { CtaBand } from '@/components/cta-band'

export const metadata: Metadata = {
  title: 'Portfolio',
  description: 'Selected residential, kitchen, bath and hospitality interiors by Maison Ardenne.',
}

export default function PortfolioPage() {
  return (
    <>
      <PageHero
        eyebrow="Portfolio"
        title={
          <>
            Homes of <em className="text-primary">character</em>
          </>
        }
        intro="Each project is a collaboration — a portrait of the people who live there, told through colour, craft and collected pieces."
      />
      <PortfolioGrid />
      <CtaBand />
    </>
  )
}
```

## `app/services/page.tsx`

```tsx
import type { Metadata } from 'next'
import Image from 'next/image'
import { Check } from 'lucide-react'
import { PageHero } from '@/components/page-hero'
import { SectionHeading } from '@/components/ornament'
import { CtaBand } from '@/components/cta-band'
import { process, services } from '@/lib/services'

export const metadata: Metadata = {
  title: 'Services',
  description: 'Full interior design, decoration, kitchens & bathrooms, and hospitality design by Maison Ardenne.',
}

const serviceImages = [
  '/images/project-townhouse.png',
  '/images/project-bedroom.png',
  '/images/project-kitchen.png',
  '/images/project-entry.png',
]

export default function ServicesPage() {
  return (
    <>
      <PageHero
        eyebrow="Services"
        title={
          <>
            Considered design, <em className="text-primary">beautifully</em> delivered
          </>
        }
        intro="From a single room to an entire house or hotel, we offer a complete, personal service — designed with care and realised with the finest craftspeople."
      />

      <section className="mx-auto max-w-7xl px-6 py-20 md:py-28 lg:px-10">
        <ol className="space-y-24 md:space-y-32">
          {services.map((s, i) => (
            <li key={s.title} className="grid items-center gap-10 md:grid-cols-12 md:gap-16">
              <div className={`relative aspect-[4/3] md:col-span-6 ${i % 2 === 1 ? 'md:order-2' : ''}`}>
                <Image src={serviceImages[i]} alt="" fill sizes="(min-width: 768px) 50vw, 100vw" className="object-cover" />
              </div>
              <div className="md:col-span-5 md:col-start-auto">
                <span className="font-serif text-6xl italic text-accent">{s.number}</span>
                <h2 className="mt-4 text-4xl md:text-5xl">{s.title}</h2>
                <p className="mt-6 leading-relaxed text-muted-foreground md:text-lg">{s.description}</p>
                <ul className="mt-8 grid gap-3 sm:grid-cols-2">
                  {s.includes.map((item) => (
                    <li key={item} className="flex items-start gap-3 text-sm">
                      <Check className="mt-0.5 size-4 shrink-0 text-primary" aria-hidden="true" />
                      {item}
                    </li>
                  ))}
                </ul>
              </div>
            </li>
          ))}
        </ol>
      </section>

      <section className="border-y border-border bg-card">
        <div className="mx-auto max-w-7xl px-6 py-24 md:py-32 lg:px-10">
          <SectionHeading
            align="center"
            eyebrow="Our Process"
            title="Four chapters, one story"
            intro="A clear, collaborative journey so you always know what comes next."
          />
          <ol className="mt-16 grid gap-px overflow-hidden border border-border bg-border sm:grid-cols-2 lg:grid-cols-4">
            {process.map((p) => (
              <li key={p.step} className="bg-card p-8 md:p-10">
                <span className="font-serif text-5xl text-primary">{p.step}</span>
                <h3 className="mt-6 text-2xl">{p.title}</h3>
                <p className="mt-3 text-sm leading-relaxed text-muted-foreground">{p.text}</p>
              </li>
            ))}
          </ol>
        </div>
      </section>

      <section className="mx-auto max-w-3xl px-6 py-24 text-center">
        <p className="eyebrow text-accent">Investment</p>
        <p className="mt-6 font-serif text-3xl leading-snug md:text-4xl">
          Our design fees are agreed transparently at the outset, with full projects typically beginning from €150,000
          in total investment.
        </p>
      </section>

      <CtaBand />
    </>
  )
}
```

## `components/contact-form.tsx`

```tsx
'use client'

import { useActionState } from 'react'
import { submitEnquiry, type ContactState } from '@/app/contact/actions'
import { cn } from '@/lib/utils'

const fieldClass =
  'mt-2 w-full border-0 border-b border-border bg-transparent px-0 py-3 text-base text-foreground placeholder:text-muted-foreground/70 focus:border-primary focus:outline-none focus:ring-0 transition-colors'

function Field({
  id,
  label,
  error,
  children,
}: {
  id: string
  label: string
  error?: string
  children: React.ReactNode
}) {
  return (
    <div>
      <label htmlFor={id} className="eyebrow text-muted-foreground">
        {label}
      </label>
      {children}
      {error && (
        <p id={`${id}-error`} className="mt-2 text-sm text-destructive">
          {error}
        </p>
      )}
    </div>
  )
}

export function ContactForm() {
  const [state, action, pending] = useActionState<ContactState, FormData>(submitEnquiry, { status: 'idle' })

  if (state.status === 'success') {
    return (
      <div role="status" className="border border-accent/50 bg-card p-10 text-center">
        <p className="eyebrow text-accent">Enquiry received</p>
        <p className="mt-4 font-serif text-3xl leading-snug">{state.message}</p>
      </div>
    )
  }

  const e = state.errors ?? {}

  return (
    <form action={action} noValidate className="space-y-8">
      <div className="grid gap-8 sm:grid-cols-2">
        <Field id="name" label="Full name" error={e.name}>
          <input
            id="name"
            name="name"
            autoComplete="name"
            required
            aria-invalid={!!e.name}
            aria-describedby={e.name ? 'name-error' : undefined}
            className={cn(fieldClass, e.name && 'border-destructive')}
          />
        </Field>
        <Field id="email" label="Email" error={e.email}>
          <input
            id="email"
            name="email"
            type="email"
            autoComplete="email"
            required
            aria-invalid={!!e.email}
            aria-describedby={e.email ? 'email-error' : undefined}
            className={cn(fieldClass, e.email && 'border-destructive')}
          />
        </Field>
      </div>
      <div className="grid gap-8 sm:grid-cols-2">
        <Field id="type" label="Project type">
          <select id="type" name="type" className={cn(fieldClass, 'cursor-pointer')} defaultValue="Full Interior Design">
            <option>Full Interior Design</option>
            <option>Decoration & Furnishing</option>
            <option>Kitchens & Bathrooms</option>
            <option>Hospitality</option>
          </select>
        </Field>
        <Field id="location" label="Project location">
          <input id="location" name="location" placeholder="City, country" className={fieldClass} />
        </Field>
      </div>
      <Field id="message" label="Tell us about your project" error={e.message}>
        <textarea
          id="message"
          name="message"
          rows={5}
          required
          aria-invalid={!!e.message}
          aria-describedby={e.message ? 'message-error' : undefined}
          placeholder="The rooms, the timeline, and how you hope it will feel…"
          className={cn(fieldClass, 'resize-none', e.message && 'border-destructive')}
        />
      </Field>
      {state.status === 'error' && state.message && (
        <p role="alert" className="text-sm text-destructive">
          {state.message}
        </p>
      )}
      <button
        type="submit"
        disabled={pending}
        className="eyebrow w-full bg-primary px-8 py-4 text-primary-foreground transition-colors hover:bg-foreground disabled:opacity-60 sm:w-auto"
      >
        {pending ? 'Sending…' : 'Send Enquiry'}
      </button>
    </form>
  )
}
```

## `components/cta-band.tsx`

```tsx
import Link from 'next/link'
import { Ornament } from '@/components/ornament'

export function CtaBand() {
  return (
    <section className="bg-secondary text-secondary-foreground">
      <div className="mx-auto max-w-4xl px-6 py-24 text-center md:py-32">
        <p className="eyebrow text-accent">Begin a Project</p>
        <h2 className="mt-6 text-4xl leading-tight md:text-6xl">
          Let us create a home that <em className="text-accent">feels like you</em>.
        </h2>
        <Ornament className="mt-8 justify-center" />
        <p className="mx-auto mt-8 max-w-xl leading-relaxed text-secondary-foreground/80">
          We take on a limited number of commissions each year so every client receives our full attention.
        </p>
        <div className="mt-10 flex flex-col items-center justify-center gap-4 sm:flex-row">
          <Link
            href="/contact"
            className="eyebrow bg-accent px-8 py-4 text-accent-foreground transition-colors hover:bg-background"
          >
            Book a Consultation
          </Link>
          <Link
            href="/portfolio"
            className="eyebrow border border-secondary-foreground/40 px-8 py-4 transition-colors hover:border-accent hover:text-accent"
          >
            View the Portfolio
          </Link>
        </div>
      </div>
    </section>
  )
}
```

## `components/home/hero.tsx`

```tsx
import Image from 'next/image'
import Link from 'next/link'
import { ArrowRight } from 'lucide-react'
import { Ornament } from '@/components/ornament'

export function HomeHero() {
  return (
    <section className="mx-auto grid max-w-7xl items-center gap-12 px-6 pb-20 pt-12 md:pt-16 lg:grid-cols-12 lg:gap-16 lg:px-10 lg:pb-28">
      <div className="lg:col-span-5">
        <p className="eyebrow text-accent">Interior Design Studio · Paris & London</p>
        <h1 className="mt-6 text-5xl leading-[1.02] sm:text-6xl xl:text-7xl">
          Rooms with <em className="text-primary">memory</em>, made for living.
        </h1>
        <Ornament className="mt-8" />
        <p className="mt-8 max-w-md text-pretty text-lg leading-relaxed text-muted-foreground">
          For fifteen years Maison Ardenne has designed timeless, quietly luxurious homes — rooms layered with craft,
          history and warmth.
        </p>
        <div className="mt-10 flex flex-col gap-4 sm:flex-row sm:items-center">
          <Link
            href="/portfolio"
            className="eyebrow inline-flex items-center justify-center gap-3 whitespace-nowrap bg-primary px-8 py-4 text-primary-foreground transition-colors hover:bg-foreground"
          >
            Explore Our Work <ArrowRight className="size-4" aria-hidden="true" />
          </Link>
          <Link href="/contact" className="eyebrow whitespace-nowrap px-2 py-4 text-foreground underline decoration-accent underline-offset-8 hover:text-primary">
            Book a Consultation
          </Link>
        </div>
      </div>

      <div className="relative isolate lg:col-span-7">
        <div className="relative aspect-[4/5] overflow-hidden rounded-t-[999px] sm:aspect-[5/6] lg:aspect-[6/7]">
          <Image
            src="/images/hero-salon.png"
            alt="A Parisian salon with an oxblood velvet sofa, brass lamp and tall windows in soft light"
            fill
            priority
            sizes="(min-width: 1024px) 58vw, 100vw"
            className="object-cover"
          />
        </div>
        <div
          aria-hidden="true"
          className="pointer-events-none absolute -inset-3 -z-10 rounded-t-[999px] border border-accent/50 md:-inset-4"
        />
        <div className="absolute -bottom-6 left-6 bg-card px-6 py-5 shadow-sm md:left-auto md:right-8">
          <p className="eyebrow text-[0.62rem] text-muted-foreground">Featured</p>
          <p className="mt-1 font-serif text-xl">Rue de Lisle Salon, Paris</p>
        </div>
      </div>
    </section>
  )
}
```

## `components/home/sections.tsx`

```tsx
import Image from 'next/image'
import Link from 'next/link'
import { ArrowRight } from 'lucide-react'
import { Ornament, SectionHeading } from '@/components/ornament'
import { ProjectCard } from '@/components/project-card'
import { projects } from '@/lib/projects'
import { services } from '@/lib/services'

const stats = [
  { value: '15', label: 'Years of practice' },
  { value: '140+', label: 'Homes completed' },
  { value: '60', label: 'Artisan partners' },
]

export function Philosophy() {
  return (
    <section className="border-y border-border bg-card">
      <div className="mx-auto grid max-w-7xl gap-12 px-6 py-24 md:grid-cols-12 md:py-32 lg:px-10">
        <div className="md:col-span-5">
          <p className="eyebrow text-accent">Our Philosophy</p>
          <h2 className="mt-4 text-4xl leading-[1.08] md:text-5xl">
            A home should feel <em>collected</em>, never decorated.
          </h2>
        </div>
        <div className="md:col-span-6 md:col-start-7">
          <p className="font-serif text-2xl leading-snug text-foreground/90 md:text-3xl">
            We believe in natural materials, honest craftsmanship and rooms that grow more beautiful with time.
          </p>
          <p className="mt-6 leading-relaxed text-muted-foreground">
            Every project begins with listening. We pair antiques with bespoke pieces, rich colour with quiet neutrals,
            and always design for the way you truly live — the morning light, the evening fire, the table where everyone
            gathers.
          </p>
          <dl className="mt-12 grid grid-cols-3 gap-6 border-t border-border pt-8">
            {stats.map((s) => (
              <div key={s.label}>
                <dt className="sr-only">{s.label}</dt>
                <dd>
                  <span className="block font-serif text-4xl text-primary md:text-5xl">{s.value}</span>
                  <span className="mt-2 block text-xs uppercase tracking-[0.2em] text-muted-foreground">{s.label}</span>
                </dd>
              </div>
            ))}
          </dl>
        </div>
      </div>
    </section>
  )
}

export function FeaturedProjects() {
  const [first, second, third] = [projects[1], projects[3], projects[5]]
  return (
    <section className="mx-auto max-w-7xl px-6 py-24 md:py-32 lg:px-10">
      <div className="flex flex-col justify-between gap-8 md:flex-row md:items-end">
        <SectionHeading
          eyebrow="Selected Work"
          title="Recent commissions"
          intro="A glimpse of homes and hotels shaped with our clients across France and Britain."
        />
        <Link
          href="/portfolio"
          className="eyebrow inline-flex shrink-0 items-center gap-3 text-primary underline decoration-accent underline-offset-8"
        >
          Full Portfolio <ArrowRight className="size-4" aria-hidden="true" />
        </Link>
      </div>
      <div className="mt-16 grid gap-x-8 gap-y-16 md:grid-cols-12">
        <div className="md:col-span-7">
          <ProjectCard project={first} aspect="landscape" sizes="(min-width: 768px) 58vw, 100vw" />
        </div>
        <div className="md:col-span-5 md:mt-32">
          <ProjectCard project={second} sizes="(min-width: 768px) 42vw, 100vw" />
        </div>
        <div className="md:col-span-5 md:col-start-2">
          <ProjectCard project={third} sizes="(min-width: 768px) 42vw, 100vw" />
        </div>
        <div className="flex flex-col justify-center md:col-span-5 md:col-start-8">
          <Ornament />
          <blockquote className="mt-8">
            <p className="font-serif text-3xl italic leading-snug md:text-4xl">
              {'“'}Our guests tell us it feels like coming home.{'”'}
            </p>
            <footer className="eyebrow mt-6 text-muted-foreground">Hôtel Belle Rive, Lake Annecy</footer>
          </blockquote>
        </div>
      </div>
    </section>
  )
}

export function ServicesPreview() {
  return (
    <section className="bg-muted/60">
      <div className="mx-auto grid max-w-7xl gap-16 px-6 py-24 md:py-32 lg:grid-cols-12 lg:px-10">
        <div className="lg:col-span-5">
          <div className="relative aspect-[4/5] overflow-hidden">
            <Image
              src="/images/studio-materials.png"
              alt="Fabric swatches, brass hardware, marble and oak samples laid out on a linen table"
              fill
              sizes="(min-width: 1024px) 40vw, 100vw"
              className="object-cover"
            />
          </div>
        </div>
        <div className="lg:col-span-6 lg:col-start-7">
          <SectionHeading
            eyebrow="What We Do"
            title="Services shaped around you"
            intro="Whether a single room or an entire house, our approach is the same: thoughtful, personal and beautifully made."
          />
          <ul className="mt-12 divide-y divide-border border-y border-border">
            {services.map((s) => (
              <li key={s.title} className="flex gap-6 py-6">
                <span className="w-8 shrink-0 font-serif text-xl italic text-accent">{s.number}</span>
                <div>
                  <h3 className="text-2xl">{s.title}</h3>
                  <p className="mt-1 text-muted-foreground">{s.short}</p>
                </div>
              </li>
            ))}
          </ul>
          <Link
            href="/services"
            className="eyebrow mt-10 inline-flex items-center gap-3 bg-foreground px-8 py-4 text-background transition-colors hover:bg-primary"
          >
            Our Services <ArrowRight className="size-4" aria-hidden="true" />
          </Link>
        </div>
      </div>
    </section>
  )
}

export function Press() {
  const names = ['Architectural Digest', 'World of Interiors', 'House & Garden', 'Elle Décor', 'Vogue Living']
  return (
    <section aria-labelledby="press-heading" className="mx-auto max-w-7xl px-6 py-16 lg:px-10">
      <h2 id="press-heading" className="eyebrow text-center text-muted-foreground">
        As featured in
      </h2>
      <ul className="mt-8 flex flex-wrap items-center justify-center gap-x-12 gap-y-4">
        {names.map((n) => (
          <li key={n} className="font-serif text-xl italic text-foreground/60 md:text-2xl">
            {n}
          </li>
        ))}
      </ul>
    </section>
  )
}
```

## `components/logo.tsx`

```tsx
import { cn } from '@/lib/utils'

export function Logo({ className, inverted = false }: { className?: string; inverted?: boolean }) {
  return (
    <span className={cn('flex items-center gap-3', className)}>
      <span
        aria-hidden="true"
        className={cn(
          'flex size-10 items-center justify-center rounded-full border font-serif text-lg italic',
          inverted ? 'border-accent text-accent' : 'border-primary text-primary',
        )}
      >
        MA
      </span>
      <span className="flex flex-col leading-none">
        <span className="font-serif text-2xl tracking-wide">Maison Ardenne</span>
        <span className={cn('mt-1 text-[0.6rem] uppercase tracking-[0.35em]', inverted ? 'text-accent' : 'text-muted-foreground')}>
          Interiors · Est. 2009
        </span>
      </span>
    </span>
  )
}
```

## `components/ornament.tsx`

```tsx
import { cn } from '@/lib/utils'

export function Ornament({ className }: { className?: string }) {
  return (
    <div aria-hidden="true" className={cn('flex items-center gap-3 text-accent', className)}>
      <span className="h-px w-12 bg-current opacity-60" />
      <svg viewBox="0 0 12 12" className="size-2.5 fill-current">
        <path d="M6 0 L12 6 L6 12 L0 6 Z" />
      </svg>
      <span className="h-px w-12 bg-current opacity-60" />
    </div>
  )
}

export function SectionHeading({
  eyebrow,
  title,
  intro,
  align = 'left',
  className,
}: {
  eyebrow: string
  title: React.ReactNode
  intro?: string
  align?: 'left' | 'center'
  className?: string
}) {
  const centered = align === 'center'
  return (
    <div className={cn('max-w-2xl', centered && 'mx-auto text-center', className)}>
      <p className="eyebrow text-accent">{eyebrow}</p>
      <h2 className="mt-4 text-4xl leading-[1.08] md:text-5xl">{title}</h2>
      {centered && <Ornament className="mt-6 justify-center" />}
      {intro && <p className="mt-6 text-pretty leading-relaxed text-muted-foreground md:text-lg">{intro}</p>}
    </div>
  )
}
```

## `components/page-hero.tsx`

```tsx
import { Ornament } from '@/components/ornament'

export function PageHero({ eyebrow, title, intro }: { eyebrow: string; title: React.ReactNode; intro: string }) {
  return (
    <section className="relative overflow-hidden border-b border-border">
      <div className="mx-auto max-w-4xl px-6 pb-20 pt-20 text-center md:pb-28 md:pt-28">
        <p className="eyebrow text-accent">{eyebrow}</p>
        <h1 className="mt-6 text-5xl leading-[1.02] md:text-7xl">{title}</h1>
        <Ornament className="mt-8 justify-center" />
        <p className="mx-auto mt-8 max-w-2xl text-pretty text-lg leading-relaxed text-muted-foreground">{intro}</p>
      </div>
    </section>
  )
}
```

## `components/portfolio-grid.tsx`

```tsx
'use client'

import { useState } from 'react'
import { categories, projects } from '@/lib/projects'
import { ProjectCard } from '@/components/project-card'
import { cn } from '@/lib/utils'

export function PortfolioGrid() {
  const [active, setActive] = useState<(typeof categories)[number]>('All')
  const visible = active === 'All' ? projects : projects.filter((p) => p.category === active)

  return (
    <section className="mx-auto max-w-7xl px-6 py-16 md:py-24 lg:px-10">
      <div role="group" aria-label="Filter projects by category" className="flex flex-wrap justify-center gap-2">
        {categories.map((c) => (
          <button
            key={c}
            type="button"
            aria-pressed={active === c}
            onClick={() => setActive(c)}
            className={cn(
              'eyebrow border px-5 py-2.5 transition-colors',
              active === c
                ? 'border-primary bg-primary text-primary-foreground'
                : 'border-border text-foreground/75 hover:border-foreground hover:text-foreground',
            )}
          >
            {c}
          </button>
        ))}
      </div>
      <p className="sr-only" aria-live="polite">
        Showing {visible.length} projects
      </p>
      <div className="mt-16 grid gap-x-8 gap-y-16 sm:grid-cols-2 lg:grid-cols-3">
        {visible.map((p, i) => (
          <div key={p.slug} className={cn(i % 3 === 1 && 'lg:mt-16')}>
            <ProjectCard project={p} />
          </div>
        ))}
      </div>
    </section>
  )
}
```

## `components/project-card.tsx`

```tsx
import Image from 'next/image'
import Link from 'next/link'
import type { Project } from '@/lib/projects'
import { cn } from '@/lib/utils'

export function ProjectCard({
  project,
  aspect = 'portrait',
  sizes = '(min-width: 1024px) 33vw, (min-width: 640px) 50vw, 100vw',
}: {
  project: Project
  aspect?: 'portrait' | 'landscape'
  sizes?: string
}) {
  return (
    <Link href={`/portfolio/${project.slug}`} className="group block">
      <div className={cn('relative overflow-hidden bg-muted', aspect === 'portrait' ? 'aspect-[4/5]' : 'aspect-[3/2]')}>
        <Image
          src={project.image}
          alt={`${project.title}, ${project.location}`}
          fill
          sizes={sizes}
          className="object-cover transition-transform duration-[1200ms] ease-out group-hover:scale-[1.04]"
        />
        <span className="eyebrow absolute left-4 top-4 bg-background/90 px-3 py-1.5 text-[0.62rem] text-foreground">
          {project.category}
        </span>
      </div>
      <div className="mt-5 flex items-baseline justify-between gap-4">
        <h3 className="text-2xl leading-tight transition-colors group-hover:text-primary md:text-3xl">{project.title}</h3>
        <span className="shrink-0 text-sm text-muted-foreground">{project.year}</span>
      </div>
      <p className="mt-1 text-sm text-muted-foreground">{project.location}</p>
    </Link>
  )
}
```

## `components/site-footer.tsx`

```tsx
import Link from 'next/link'
import { Logo } from '@/components/logo'

const columns = [
  {
    title: 'Studio',
    links: [
      { href: '/about', label: 'About' },
      { href: '/services', label: 'Services' },
      { href: '/portfolio', label: 'Portfolio' },
      { href: '/contact', label: 'Contact' },
    ],
  },
  {
    title: 'Follow',
    links: [
      { href: 'https://instagram.com', label: 'Instagram' },
      { href: 'https://pinterest.com', label: 'Pinterest' },
      { href: 'https://linkedin.com', label: 'LinkedIn' },
    ],
  },
]

export function SiteFooter() {
  return (
    <footer className="bg-foreground text-background">
      <div className="mx-auto grid max-w-7xl gap-12 px-6 py-20 md:grid-cols-12 lg:px-10">
        <div className="md:col-span-5">
          <Logo inverted />
          <p className="mt-6 max-w-sm leading-relaxed text-background/70">
            Timeless, quietly luxurious interiors for homes and small hotels across Europe.
          </p>
        </div>
        <div className="md:col-span-3">
          <h2 className="eyebrow text-accent">Visit</h2>
          <address className="mt-5 not-italic leading-relaxed text-background/80">
            14 Rue Jacob
            <br />
            75006 Paris
            <br />
            <a href="tel:+33142000000" className="hover:text-accent">
              +33 1 42 00 00 00
            </a>
            <br />
            <a href="mailto:studio@maisonardenne.com" className="hover:text-accent">
              studio@maisonardenne.com
            </a>
          </address>
        </div>
        {columns.map((col) => (
          <div key={col.title} className="md:col-span-2">
            <h2 className="eyebrow text-accent">{col.title}</h2>
            <ul className="mt-5 space-y-3">
              {col.links.map((link) => (
                <li key={link.label}>
                  <Link href={link.href} className="text-background/80 transition-colors hover:text-accent">
                    {link.label}
                  </Link>
                </li>
              ))}
            </ul>
          </div>
        ))}
      </div>
      <div className="border-t border-background/15">
        <div className="mx-auto flex max-w-7xl flex-col gap-2 px-6 py-6 text-xs text-background/60 sm:flex-row sm:justify-between lg:px-10">
          <p>© {new Date().getFullYear()} Maison Ardenne Interiors</p>
          <p>Paris · London · By appointment</p>
        </div>
      </div>
    </footer>
  )
}
```

## `components/site-header.tsx`

```tsx
'use client'

import Link from 'next/link'
import { usePathname } from 'next/navigation'
import { useState } from 'react'
import { Menu, X } from 'lucide-react'
import { cn } from '@/lib/utils'
import { Logo } from '@/components/logo'

export const navItems = [
  { href: '/portfolio', label: 'Portfolio' },
  { href: '/services', label: 'Services' },
  { href: '/about', label: 'About' },
  { href: '/contact', label: 'Contact' },
]

export function SiteHeader() {
  const pathname = usePathname()
  const [open, setOpen] = useState(false)

  return (
    <header className="sticky top-0 z-50 border-b border-border/70 bg-background/90 backdrop-blur-md">
      <div className="mx-auto flex h-20 max-w-7xl items-center justify-between px-6 lg:px-10">
        <Link href="/" className="shrink-0" onClick={() => setOpen(false)} aria-label="Maison Ardenne, home">
          <Logo />
        </Link>

        <nav aria-label="Primary" className="hidden md:block">
          <ul className="flex items-center gap-10">
            {navItems.map((item) => {
              const active = pathname.startsWith(item.href)
              return (
                <li key={item.href}>
                  <Link
                    href={item.href}
                    aria-current={active ? 'page' : undefined}
                    className={cn(
                      'eyebrow relative py-2 text-foreground/75 transition-colors hover:text-foreground',
                      'after:absolute after:inset-x-0 after:-bottom-0.5 after:h-px after:origin-left after:scale-x-0 after:bg-accent after:transition-transform hover:after:scale-x-100',
                      active && 'text-foreground after:scale-x-100',
                    )}
                  >
                    {item.label}
                  </Link>
                </li>
              )
            })}
          </ul>
        </nav>

        <Link
          href="/contact"
          className="eyebrow hidden border border-primary px-5 py-3 text-primary transition-colors hover:bg-primary hover:text-primary-foreground lg:inline-block"
        >
          Book a Consultation
        </Link>

        <button
          type="button"
          className="-mr-2 p-2 md:hidden"
          aria-expanded={open}
          aria-controls="mobile-nav"
          aria-label={open ? 'Close menu' : 'Open menu'}
          onClick={() => setOpen((v) => !v)}
        >
          {open ? <X className="size-6" /> : <Menu className="size-6" />}
        </button>
      </div>

      {open && (
        <nav id="mobile-nav" aria-label="Mobile" className="border-t border-border bg-background md:hidden">
          <ul className="flex flex-col px-6 py-6">
            {[{ href: '/', label: 'Home' }, ...navItems].map((item) => (
              <li key={item.href} className="border-b border-border/60 last:border-0">
                <Link
                  href={item.href}
                  onClick={() => setOpen(false)}
                  aria-current={pathname === item.href ? 'page' : undefined}
                  className="block py-4 font-serif text-3xl"
                >
                  {item.label}
                </Link>
              </li>
            ))}
          </ul>
          <div className="px-6 pb-8">
            <Link
              href="/contact"
              onClick={() => setOpen(false)}
              className="eyebrow block bg-primary px-5 py-4 text-center text-primary-foreground"
            >
              Book a Consultation
            </Link>
          </div>
        </nav>
      )}
    </header>
  )
}
```

## `lib/projects.ts`

```ts
export type Project = {
  slug: string
  title: string
  location: string
  year: string
  category: 'Residential' | 'Kitchen & Bath' | 'Hospitality'
  image: string
  gallery: string[]
  summary: string
  brief: string
  approach: string
  palette: { name: string; hex: string }[]
  details: { label: string; value: string }[]
  quote?: { text: string; author: string }
}

export const projects: Project[] = [
  {
    slug: 'rue-de-lisle-salon',
    title: 'Rue de Lisle Salon',
    location: 'Paris, France',
    year: '2025',
    category: 'Residential',
    image: '/images/hero-salon.png',
    gallery: ['/images/hero-salon.png', '/images/project-entry.png', '/images/project-bedroom.png'],
    summary: 'A Haussmann apartment restored to its original grace, softened with velvet and lamplight.',
    brief:
      'The clients wanted a home that honoured the apartment’s 1880s mouldings and parquet while feeling relaxed enough for children, dogs, and long Sunday lunches.',
    approach:
      'We stripped back decades of paint to reveal the plasterwork, then layered warm limewash, deep claret upholstery and antique walnut. Lighting was reconsidered room by room so evenings glow rather than glare.',
    palette: [
      { name: 'Limewash Ivory', hex: '#EFE6D8' },
      { name: 'Claret Velvet', hex: '#6B2B2B' },
      { name: 'Aged Brass', hex: '#A9844F' },
      { name: 'Walnut', hex: '#5A4232' },
    ],
    details: [
      { label: 'Scope', value: 'Full renovation & furnishing' },
      { label: 'Size', value: '210 m²' },
      { label: 'Duration', value: '14 months' },
      { label: 'Photography', value: 'Studio archive' },
    ],
    quote: {
      text: 'It feels as though the apartment has always looked this way — only now it finally feels like ours.',
      author: 'Homeowner, Rue de Lisle',
    },
  },
  {
    slug: 'holland-park-townhouse',
    title: 'Holland Park Townhouse',
    location: 'London, UK',
    year: '2024',
    category: 'Residential',
    image: '/images/project-townhouse.png',
    gallery: ['/images/project-townhouse.png', '/images/project-library.png', '/images/project-kitchen.png'],
    summary: 'A Georgian family house wrapped in deep greens, painted panelling and collected art.',
    brief:
      'A five-storey townhouse needed to work for a growing family and frequent entertaining without losing its period character.',
    approach:
      'Each floor was given its own mood: an enveloping forest-green dining room, a calm upper floor in stone and linen, and a library that doubles as a snug.',
    palette: [
      { name: 'Forest Panel', hex: '#2F4A3D' },
      { name: 'Parchment', hex: '#EDE3D1' },
      { name: 'Cane', hex: '#C9A774' },
      { name: 'Oxblood', hex: '#5E2424' },
    ],
    details: [
      { label: 'Scope', value: 'Interior architecture & decoration' },
      { label: 'Size', value: '420 m²' },
      { label: 'Duration', value: '20 months' },
      { label: 'Collaborators', value: 'Hart & Sons Joinery' },
    ],
    quote: {
      text: 'Every room has a story now. Guests always linger in the dining room.',
      author: 'Homeowner, Holland Park',
    },
  },
  {
    slug: 'cotswold-farmhouse-kitchen',
    title: 'Cotswold Farmhouse Kitchen',
    location: 'Gloucestershire, UK',
    year: '2024',
    category: 'Kitchen & Bath',
    image: '/images/project-kitchen.png',
    gallery: ['/images/project-kitchen.png', '/images/project-bath.png', '/images/studio-materials.png'],
    summary: 'Honed marble, unlacquered brass and handmade green tile for a working country kitchen.',
    brief:
      'A dark, dated kitchen at the heart of a 17th-century farmhouse needed to become the bright everyday centre of family life.',
    approach:
      'We opened the room to the garden, designed bespoke shaker cabinetry, and chose materials that age beautifully — brass that patinates, marble that softens with use.',
    palette: [
      { name: 'Warm White', hex: '#F3EEE4' },
      { name: 'Glazed Green', hex: '#3E5A48' },
      { name: 'Honed Marble', hex: '#D9D2C6' },
      { name: 'Brass', hex: '#B08D57' },
    ],
    details: [
      { label: 'Scope', value: 'Kitchen design & joinery' },
      { label: 'Size', value: '48 m²' },
      { label: 'Duration', value: '9 months' },
      { label: 'Collaborators', value: 'Fired Earth Studio' },
    ],
  },
  {
    slug: 'the-reading-room',
    title: 'The Reading Room',
    location: 'Bath, UK',
    year: '2023',
    category: 'Residential',
    image: '/images/project-library.png',
    gallery: ['/images/project-library.png', '/images/project-townhouse.png', '/images/project-entry.png'],
    summary: 'A collector’s library of walnut, worn leather and green-shaded lamps.',
    brief: 'A retired professor wanted a room to house four thousand books and a lifetime of objects.',
    approach:
      'Floor-to-ceiling walnut shelving, a restored stone fireplace and layered antique rugs create a room built for slow afternoons.',
    palette: [
      { name: 'Walnut', hex: '#4E3829' },
      { name: 'Leather', hex: '#6B2B2B' },
      { name: 'Banker Green', hex: '#2F4A3D' },
      { name: 'Bath Stone', hex: '#D8C8AA' },
    ],
    details: [
      { label: 'Scope', value: 'Library & study' },
      { label: 'Size', value: '36 m²' },
      { label: 'Duration', value: '7 months' },
      { label: 'Collaborators', value: 'Bath Antique Rug Co.' },
    ],
  },
  {
    slug: 'claret-bath-suite',
    title: 'The Claret Bath Suite',
    location: 'Edinburgh, UK',
    year: '2023',
    category: 'Kitchen & Bath',
    image: '/images/project-bath.png',
    gallery: ['/images/project-bath.png', '/images/project-bedroom.png', '/images/studio-materials.png'],
    summary: 'A painted cast-iron tub, checkerboard marble and soft linen in a New Town flat.',
    brief: 'The clients asked for a bathroom that felt like a room — somewhere to sit, read and stay a while.',
    approach:
      'We centred the space on a claret-painted bateau tub, laid a cream and taupe marble checkerboard and added an armchair by the window.',
    palette: [
      { name: 'Claret', hex: '#6B2B2B' },
      { name: 'Cream Marble', hex: '#EFE7DA' },
      { name: 'Taupe', hex: '#A8988A' },
      { name: 'Brass', hex: '#A9844F' },
    ],
    details: [
      { label: 'Scope', value: 'Bathroom & dressing room' },
      { label: 'Size', value: '22 m²' },
      { label: 'Duration', value: '5 months' },
      { label: 'Photography', value: 'Studio archive' },
    ],
  },
  {
    slug: 'hotel-belle-rive',
    title: 'Hôtel Belle Rive',
    location: 'Lake Annecy, France',
    year: '2025',
    category: 'Hospitality',
    image: '/images/project-entry.png',
    gallery: ['/images/project-entry.png', '/images/project-bedroom.png', '/images/hero-salon.png'],
    summary: 'A lakeside boutique hotel with the welcome of a private country house.',
    brief:
      'A family-owned hotel of twelve rooms wanted to feel residential and intimate, never corporate.',
    approach:
      'Limestone floors, a sweeping restored staircase, and bedrooms dressed in linen and rust create a sense of arriving at a friend’s home.',
    palette: [
      { name: 'Limestone', hex: '#E4DACB' },
      { name: 'Rust', hex: '#9A4E33' },
      { name: 'Linen', hex: '#F1EBE0' },
      { name: 'Dark Oak', hex: '#3E2F25' },
    ],
    details: [
      { label: 'Scope', value: 'Public spaces & 12 suites' },
      { label: 'Size', value: '1,800 m²' },
      { label: 'Duration', value: '26 months' },
      { label: 'Operator', value: 'Famille Rive' },
    ],
    quote: {
      text: 'Our guests tell us it feels like coming home. That is exactly what we hoped for.',
      author: 'Owner, Hôtel Belle Rive',
    },
  },
]

export const categories = ['All', 'Residential', 'Kitchen & Bath', 'Hospitality'] as const

export function getProject(slug: string) {
  return projects.find((p) => p.slug === slug)
}
```

## `lib/services.ts`

```ts
export const services = [
  {
    number: 'I',
    title: 'Full Interior Design',
    short: 'From first sketch to final cushion — a complete, considered home.',
    description:
      'Our signature service. We guide every decision across architecture, joinery, lighting, furniture, art and textiles, managing contractors and installation so you simply arrive home.',
    includes: ['Concept & spatial planning', 'Bespoke joinery & detailing', 'Procurement & project management', 'Installation & styling'],
  },
  {
    number: 'II',
    title: 'Decoration & Furnishing',
    short: 'Transforming existing rooms with furniture, colour and textile.',
    description:
      'For homes that need no structural change, we compose rooms through colour, antiques, upholstery and lighting, sourcing from our network of ateliers and dealers.',
    includes: ['Colour & material schemes', 'Furniture & antique sourcing', 'Window treatments & upholstery', 'Art curation'],
  },
  {
    number: 'III',
    title: 'Kitchens & Bathrooms',
    short: 'Hard-working rooms crafted with enduring materials.',
    description:
      'Natural stone, unlacquered brass and handmade tile, designed with the precision of a cabinetmaker and the warmth of a country house.',
    includes: ['Layout & ergonomics', 'Custom cabinetry design', 'Stone & tile selection', 'Fixture specification'],
  },
  {
    number: 'IV',
    title: 'Hospitality',
    short: 'Boutique hotels and restaurants with the soul of a private home.',
    description:
      'We work with independent owners to create guest experiences that feel intimate and memorable, balancing durability with beauty.',
    includes: ['Brand & guest journey', 'Public spaces & suites', 'FF&E specification', 'Operational detailing'],
  },
]

export const process = [
  { step: '01', title: 'Consultation', text: 'We meet at your home or our studio to understand how you live, what you love and what you hope to feel.' },
  { step: '02', title: 'Concept', text: 'Mood, plans and a curated palette of materials — presented in person with physical samples.' },
  { step: '03', title: 'Design Development', text: 'Detailed drawings, bespoke pieces and a full specification, refined together until every detail is right.' },
  { step: '04', title: 'Realisation', text: 'We manage craftsmen, deliveries and installation, then style each room for the moment you walk in.' },
]
```

## `lib/utils.ts`

```ts
import { clsx, type ClassValue } from 'clsx'
import { twMerge } from 'tailwind-merge'

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}
```
