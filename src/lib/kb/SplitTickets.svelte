<script lang="ts">
  import reports from '#lib/data/reports-4471.json';
  import { clusterReports, reporterKey, uniqueById, type Report } from '#lib/kb/fingerprint';
  import { placeOf } from '#lib/kb/places';

  // One row per issue found in a merged ticket, with the ticket it now has and how that closed.
  type Filed = { url: string; outcome: string };
  let { ticket, tickets }: { ticket: number; tickets: Record<string, Filed> } = $props();

  const merged = $derived(uniqueById(reports as Report[]).filter((r) => r.mergedInto === ticket));
  const clusters = $derived(clusterReports(merged, placeOf));
  // A row with no reporter address is not a person: the 03:12 job wrote one.
  const fromPeople = (rs: Report[]) => rs.filter((r) => r.reporter.includes('@'));
  const mistagged = (place: string, rs: Report[]) => rs.filter((r) => r.product !== place).length;
  const time = (iso: string) => iso.slice(11, 16);
  const label = (url: string) => '#' + url.split('/').pop();
</script>

<table>
  <thead>
    <tr><th>issue</th><th>reports</th><th>people</th><th>tagged elsewhere</th><th>seen</th><th>ticket</th><th>outcome</th></tr>
  </thead>
  <tbody>
    {#each clusters as c (c.fingerprint)}
      {@const people = fromPeople(c.reports)}
      <tr>
        <td><code>{c.place}</code> <code>{c.code}</code></td>
        <td>{people.length}{c.reports.length > people.length ? ` + ${c.reports.length - people.length} job` : ''}</td>
        <td>{new Set(people.map((r) => reporterKey(r.reporter))).size}</td>
        <td>{mistagged(c.place, c.reports) || '—'}</td>
        <td>{time(c.firstSeen)} → {time(c.lastSeen)}</td>
        <td>
          {#if tickets[c.fingerprint]}<a href={tickets[c.fingerprint].url}>{label(tickets[c.fingerprint].url)}</a>
          {:else}<span class="none">no ticket yet</span>{/if}
        </td>
        <td>{tickets[c.fingerprint]?.outcome ?? '—'}</td>
      </tr>
    {/each}
  </tbody>
</table>
<p class="sum">{clusters.length} issues · {merged.length} unique reports · {(reports as Report[]).length - merged.length} duplicate rows in the export</p>

<style>
  .sum { color: var(--muted); font-size: 0.85rem; }
  .none { color: var(--warn); font-weight: 600; }
</style>
