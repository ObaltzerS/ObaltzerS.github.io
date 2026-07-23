<script setup lang="ts">
import { computed, ref } from 'vue';

export type ProjectStatus = 'completed' | 'in-progress' | 'planned';

export interface Project {
  title: string;
  description: string;
  status: ProjectStatus;
  tags: string[];
  links?: {
    repo?: string;
    demo?: string;
    paper?: string;
  };
  date?: string;
}

const props = defineProps<{
  projects: Project[];
}>();

const filters = [
  { id: 'all', label: 'All' },
  { id: 'completed', label: 'Completed' },
  { id: 'in-progress', label: 'In progress' },
  { id: 'planned', label: 'Planned' },
] as const;

type FilterId = (typeof filters)[number]['id'];

const active = ref<FilterId>('all');

const statusLabel: Record<ProjectStatus, string> = {
  completed: 'Completed',
  'in-progress': 'In progress',
  planned: 'Planned',
};

const filtered = computed(() => {
  if (active.value === 'all') return props.projects;
  return props.projects.filter((p) => p.status === active.value);
});

function formatDate(date?: string) {
  if (!date) return '';
  const [y, m] = date.split('-');
  if (!m) return y;
  const month = new Date(Number(y), Number(m) - 1).toLocaleString('en', {
    month: 'short',
  });
  return `${month} ${y}`;
}
</script>

<template>
  <section id="projects" class="section projects">
    <div class="container">
      <div class="head">
        <h2 class="section-title">Projects</h2>
        <div class="filters" role="tablist" aria-label="Filter projects by status">
          <button
            v-for="filter in filters"
            :key="filter.id"
            type="button"
            role="tab"
            class="filter"
            :class="{ active: active === filter.id }"
            :aria-selected="active === filter.id"
            @click="active = filter.id"
          >
            {{ filter.label }}
          </button>
        </div>
      </div>

      <p v-if="filtered.length === 0" class="empty muted">
        No projects in this category yet.
      </p>

      <ul v-else class="grid">
        <li v-for="project in filtered" :key="project.title" class="card">
          <div class="card-top">
            <span class="status" :data-status="project.status">
              {{ statusLabel[project.status] }}
            </span>
            <time v-if="project.date" class="date muted">{{ formatDate(project.date) }}</time>
          </div>
          <h3>{{ project.title }}</h3>
          <p class="desc muted">{{ project.description }}</p>
          <ul v-if="project.tags?.length" class="tags">
            <li v-for="tag in project.tags" :key="tag">{{ tag }}</li>
          </ul>
          <div class="links">
            <a
              v-if="project.links?.repo"
              :href="project.links.repo"
              target="_blank"
              rel="noreferrer"
            >Repo</a>
            <a
              v-if="project.links?.demo"
              :href="project.links.demo"
              target="_blank"
              rel="noreferrer"
            >Demo</a>
            <a
              v-if="project.links?.paper"
              :href="project.links.paper"
              target="_blank"
              rel="noreferrer"
            >Paper</a>
          </div>
        </li>
      </ul>
    </div>
  </section>
</template>

<style scoped>
.projects {
  padding-bottom: 5rem;
}

.head {
  display: flex;
  flex-wrap: wrap;
  align-items: end;
  justify-content: space-between;
  gap: 1rem;
  margin-bottom: 1.5rem;
}

.section-title {
  margin: 0;
  font-size: clamp(1.5rem, 2vw, 1.75rem);
  font-weight: 600;
  letter-spacing: -0.02em;
}

.filters {
  display: flex;
  flex-wrap: wrap;
  gap: 0.4rem;
  padding: 0.25rem;
  background: #f5f5f4;
  border: 1px solid var(--border, #e7e5e4);
  border-radius: 999px;
}

.filter {
  appearance: none;
  border: 0;
  background: transparent;
  color: var(--muted, #57534e);
  font: inherit;
  font-size: 0.9rem;
  font-weight: 500;
  padding: 0.4rem 0.85rem;
  border-radius: 999px;
  cursor: pointer;
}

.filter:hover {
  color: var(--text, #1c1917);
}

.filter.active {
  background: var(--surface, #fff);
  color: var(--text, #1c1917);
  box-shadow: 0 1px 2px rgb(0 0 0 / 6%);
}

.grid {
  list-style: none;
  margin: 0;
  padding: 0;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(17rem, 1fr));
  gap: 1rem;
}

.card {
  display: flex;
  flex-direction: column;
  gap: 0.65rem;
  background: var(--surface, #fff);
  border: 1px solid var(--border, #e7e5e4);
  border-radius: var(--radius, 12px);
  box-shadow: var(--shadow, 0 1px 2px rgb(0 0 0 / 4%), 0 8px 24px rgb(0 0 0 / 4%));
  padding: 1.25rem;
  min-height: 14rem;
}

.card-top {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 0.75rem;
}

.status {
  display: inline-flex;
  align-items: center;
  font-size: 0.75rem;
  font-weight: 600;
  letter-spacing: 0.02em;
  padding: 0.2rem 0.55rem;
  border-radius: 999px;
  background: #f5f5f4;
  color: #44403c;
}

.status[data-status='completed'] {
  background: #dcfce7;
  color: #166534;
}

.status[data-status='in-progress'] {
  background: var(--accent-soft, #dbeafe);
  color: #1e40af;
}

.status[data-status='planned'] {
  background: #fef3c7;
  color: #92400e;
}

.date {
  font-size: 0.8rem;
}

h3 {
  margin: 0;
  font-size: 1.1rem;
  letter-spacing: -0.02em;
  line-height: 1.3;
}

.desc {
  margin: 0;
  font-size: 0.95rem;
  flex: 1;
}

.tags {
  list-style: none;
  display: flex;
  flex-wrap: wrap;
  gap: 0.35rem;
  margin: 0;
  padding: 0;
}

.tags li {
  font-size: 0.75rem;
  font-weight: 500;
  color: var(--muted, #57534e);
  background: #f5f5f4;
  border-radius: 999px;
  padding: 0.2rem 0.55rem;
}

.links {
  display: flex;
  gap: 0.85rem;
  margin-top: 0.25rem;
}

.links a {
  font-size: 0.9rem;
  font-weight: 500;
}

.empty {
  margin: 0;
}

.muted {
  color: var(--muted, #57534e);
}

.container {
  width: min(100% - 2rem, var(--max, 68rem));
  margin-inline: auto;
}

.section {
  padding-block: 4rem;
}
</style>
