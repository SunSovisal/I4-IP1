<script setup lang="ts">
import { computed, ref } from "vue";
import type { CampusEvent, Category } from "../types";
import EventCard from "./EventCard.vue";

const props = defineProps<{ events: CampusEvent[] }>();
// TODO 1: declare a "book" event (same as EventCard) so the list can pass it up
const emit = defineEmits<{
  book: [id: number];
}>();
// TODO 2: two refs for the filters
//   search   -> the text typed in the search box
//   category -> Category | "all", starts at "all"
const search = ref("");
const category = ref<Category | "all">("all");

// TODO 3: a computed `visible` list:
//   - keep events of the selected category (or all)
//   - keep events whose title OR location contains the search text, ignoring case
const visible = computed(() => {
  const q = search.value.toLowerCase();
  return props.events.filter((e) => {
    const selectedCategory =
      category.value === "all" || e.category === category.value;
    const searched =
      e.title.toLowerCase().includes(q) || e.location.toLowerCase().includes(q);
    return selectedCategory && searched;
  });
});
</script>

<template>
  <section>
    <div class="filters">
      <!-- TODO 4: bind the input and the select to your refs with v-model -->
      <input
        type="search"
        placeholder="Search title or location"
        aria-label="Search"
        v-model="search"
      />
      <select aria-label="Category" v-model="category">
        <option value="all">All categories</option>
        <option value="talk">Talks</option>
        <option value="workshop">Workshops</option>
        <option value="social">Social</option>
        <option value="sport">Sport</option>
      </select>
    </div>

    <!-- TODO 5: show "<visible count> of <total> events" -->
    <p class="count">{{ visible.length }} of {{ events.length }} events</p>

    <!-- TODO 6: render one EventCard per visible event (v-for with a :key),
         pass the event as a prop and pass its "book" event up.
         When nothing is visible, show the empty message instead (v-if / v-else). -->
    <div v-if="visible.length > 0" class="grid">
        <EventCard v-for="e in visible" :key="e.id" :event="e" @book="emit('book', $event)"/>
    </div>
    <p v-else class="empty">No events match</p>
  </section>
</template>

<style scoped>
.filters {
  display: flex;
  gap: 8px;
  flex-wrap: wrap;
}
.filters input {
  flex: 1;
  min-width: 200px;
}
.count {
  color: #6b7280;
}
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(260px, 1fr));
  gap: 16px;
}
.empty {
  padding: 32px;
  text-align: center;
  background: white;
  border-radius: 10px;
}
</style>
