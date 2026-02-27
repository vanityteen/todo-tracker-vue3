<template>
  <div class="max-w-6xl mx-auto px-6 lg:px-8 py-8 lg:py-8">
    <!-- Page Header -->
    <div class="mb-8">
      <h1 class="text-2xl font-semibold text-gray-900">Today Activities</h1>
      <p class="mt-1 text-sm text-gray-500">
        Manage your habits, reminders, events, activities.
      </p>
    </div>

    <!-- Habits Section -->
    <section>
      <div class="flex items-center justify-between mb-4">
        <h2 class="text-lg font-medium text-gray-900">Your Habits</h2>
        <button class="flex items-center px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700">
          <PlusIcon class="w-5 h-5 sm:mr-2" />
          <span class="hidden sm:inline"> Add Habit </span>
        </button>
      </div>
      <div class="swiper-container">
        <swiper :slides-per-view="'auto'" :space-between="12" :grab-cursor="true" class="!pb-4">
          <swiper-slide v-for="habit in habits" :key="habit.id" class="!w-auto">
            <HabitCard v-bind="habit" class="habit-card" />
          </swiper-slide>
        </swiper>
      </div>
    </section>

    <!-- Reminders Section -->
    <section>
      <div class="flex items-center justify-between mb-4">
        <h2 class="text-lg font-medium text-gray-900">Reminders</h2>
      </div>
      <div class="space-y-4">
        <TaskItem v-for="reminder in reminders" :key="reminder.id" v-bind="reminder"
          @toggle-complete="toggleReminder(reminder.id)" @toggle-important="toggleImportant(reminder.id)"
          @edit="editReminder(reminder.id)" @delete="deleteReminder(reminder.id)" />
      </div>
    </section>

    <!-- To Do List -->
    <section>
      <div class="flex items-center justify-between mb-4">
        <h2 class="text-lg font-medium text-gray-900">To Do List</h2>
        <div class="flex items-center gap-2">
          <button v-for="status in ['To Do', 'In Progress', 'Completed']" :key="status"
            class="px-3 py-1 text-sm rounded-md" :class="[
              activeStatus === status
                ? 'bg-blue-50 text-blue-700'
                : 'text-gray-500 hover:text-gray-700 hover:bg-gray-50'
            ]" @click="activeStatus = status">
            {{ status }}
            <span class="ml-1 text-xs">
              {{ getTaskCountByStatus(status) }}
            </span>
          </button>
        </div>
      </div>
      <div class="space-y-4">
        <TaskItem v-for="task in filteredTasks" :key="task.id" v-bind="task" @toggle-complete="toggleTask(task.id)"
          @toggle-important="toggleImportant(task.id)" @edit="editTask(task.id)" @delete="deleteTask(task.id)" />
      </div>
    </section>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import HabitCard from '@/modules/habits/components/HabitCard.vue'
import TaskItem from '@/modules/tasks/components/TaskItem.vue'
import { cooking, fitness, gaming, hiking, makeup, music, outfit, photograph, reading, swimming } from '@/assets/image';
// Import Swiper Vue components
import { Swiper, SwiperSlide } from 'swiper/vue';
// Import Swiper styles
import 'swiper/css';
import { PlusIcon } from '@heroicons/vue/24/outline'

// Sample data
const habits = ref([
  {
    id: 1,
    title: 'Hiking',
    image: hiking,
    startTime: '07:00',
    endTime: '07:30'
  },
  {
    id: 2,
    title: 'Cooking',
    image: cooking,
    startTime: '09:00',
    endTime: '10:00'
  },
  {
    id: 3,
    title: 'Gaming',
    image: gaming,
    startTime: '09:00',
    endTime: '10:00'
  },
  {
    id: 4,
    title: 'Reading',
    image: reading,
    startTime: '09:00',
    endTime: '10:00'
  },
  {
    id: 5,
    title: 'Swimming',
    image: swimming,
    startTime: '09:00',
    endTime: '10:00'
  },
  {
    id: 6,
    title: 'Outfit',
    image: outfit,
    startTime: '09:00',
    endTime: '10:00'
  },
  {
    id: 7,
    title: 'Fitness',
    image: fitness,
    startTime: '09:00',
    endTime: '10:00'
  },
  {
    id: 8,
    title: 'Makeup',
    image: makeup,
    startTime: '09:00',
    endTime: '10:00'
  },
  {
    id: 9,
    title: 'Music',
    image: music,
    startTime: '09:00',
    endTime: '10:00'
  },
  {
    id: 10,
    title: 'Photograph',
    image: photograph,
    startTime: '09:00',
    endTime: '10:00'
  }
])

const reminders = ref([
  {
    id: 1,
    title: 'Gym Session Week 3',
    description: 'Day for biceps, legs, and back.',
    tag: { label: 'work', color: 'gray' },
    dueDate: new Date('2024-04-18T15:00:00'),
    isCompleted: false,
    isImportant: true
  },
  // Add more reminders...
])

const tasks = ref([
  {
    id: 1,
    title: 'UX Researching - Phase 2',
    description: 'Conduct user interview with 3 participants',
    tag: { label: 'work', color: 'gray' },
    status: 'To Do',
    isCompleted: false,
    isImportant: false
  },
  // Add more tasks...
])

const activeStatus = ref('To Do')

const filteredTasks = computed(() => {
  return tasks.value.filter(task => task.status === activeStatus.value)
})

const getTaskCountByStatus = (status: string) => {
  return tasks.value.filter(task => task.status === status).length
}

// Event handlers
const toggleReminder = (id: number) => {
  const reminder = reminders.value.find(r => r.id === id)
  if (reminder) {
    reminder.isCompleted = !reminder.isCompleted
  }
}

const toggleTask = (id: number) => {
  const task = tasks.value.find(t => t.id === id)
  if (task) {
    task.isCompleted = !task.isCompleted
    task.status = task.isCompleted ? 'Completed' : 'To Do'
  }
}

const toggleImportant = (id: number) => {
  const item = [...reminders.value, ...tasks.value].find(i => i.id === id)
  if (item) {
    item.isImportant = !item.isImportant
  }
}

const editReminder = (id: number) => {
  // Implement edit functionality
}

const deleteReminder = (id: number) => {
  reminders.value = reminders.value.filter(r => r.id !== id)
}

const editTask = (id: number) => {
  // Implement edit functionality
}

const deleteTask = (id: number) => {
  tasks.value = tasks.value.filter(t => t.id !== id)
}
</script>

<style scoped>
.swiper-container {
  overflow: hidden;
  padding: 8px 0;
}

:deep(.swiper-slide) {
  width: auto;
  padding: 0 2px;
}

:deep(.swiper-wrapper) {
  display: flex;
  align-items: center;
  padding: 8px 0;
}

.habit-card {
  max-width: 140px;
  min-width: 120px;
}
</style>