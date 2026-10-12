# kb — the support knowledge base

**kb** = knowledge base. What the support desk knows about how our products break, written
down where the code can keep it honest.

One desk takes reports for every product we run: `sched-prod` (shift planning, about 300k
people), `tram-3` (departure boards), `rota-6` (care-home rotas) and the rest. When something
goes wrong, the reports reach the desk first. This kb is how the desk triages them, what each
incident turned out to be, and why we decided what we decided.

## What's here

```
src/routes/kb/      the pages (.svx: Markdown with components)
  triage/           how the desk handles incoming reports
  incidents/        one page per big ticket
  decisions/        numbered decision records
src/lib/kb/         components and the logic they run
src/lib/layouts/    the frame every page shares
src/lib/data/       exports from the ticket system
```

## Conventions

- Words in the page, logic in `src/lib` (imported as `#lib/…`). A number that can be computed belongs in a component.
- Every page starts with frontmatter: `title`, `owner`, `status`, `reviewed`.
- A decision is never edited after it is accepted. A new record replaces it.
- A page you add is signed: its frontmatter carries your `signature` and it ends with `<RenderStamp {signature} />`, so the desk knows who wrote it.

## Owners

Support desk (Tove). Decision records: whoever signed them.

---

Exported from the `support` repo, `apps/kb/` → `src/`.

## Added: #4471 · three issues, not one

- Page source: `src/routes/kb/incidents/4471-issues/+page.svx` (signed, ends with `<RenderStamp {signature} />`)
- Component: `src/lib/kb/SplitTickets.svelte`
- The page as the desk sees it: `build/kb/incidents/4471-issues/index.html` (static build, `npm run build`), screenshot in `rendered/`
- Tickets: [#1 sched-prod E-TZ-114](https://github.com/iceener/kb-4471-a/issues/1) · [#2 tram-3 E-TZ-114](https://github.com/iceener/kb-4471-a/issues/2) · [#3 rota-6 E-SYNC-04](https://github.com/iceener/kb-4471-a/issues/3)
