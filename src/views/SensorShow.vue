<script setup lang="ts">
import { onMounted, ref, Ref } from 'vue';
import { useRoute, useRouter } from 'vue-router';
import { api } from '../services/api.service';
import { Sensor } from '../types/sensor.interface';
import { SensorEvent } from '../types/sensorEvent.interface';

const route = useRoute()
const router = useRouter()
const id = route.params.id

const sensor: Ref<Sensor> = ref({ id: "", name: "", short_name: "" })

onMounted(async () => {
  sensor.value = await api.value.getSensorById(id as string)
})

function addEvent() {
  router.push({name: "sensors.addEvent", params: {id}})
}

function editEvent(event: SensorEvent) {
  router.push({name: "sensors.editEvent", params: {id, eventId: event.id}})
}
</script>

<template>
  <h1 class="text-center text-2xl">{{ sensor.name }}</h1>
  <div class="flex justify-around items-center my-2">
    <h2 class="text-xl font-semibold">Events</h2>
    <button class="bg-blue-400 py-2 px-3 rounded drop-shadow" @click="addEvent">Add</button>
  </div>

  <!--Events list-->
    <div class="grid grid-cols-2 gap-2" v-if="sensor.events">
      <ul v-for="event in sensor.events" class="bg-orange-200 p-2 rounded-sm drop-shadow hover:cursor-pointer" @click="editEvent(event)">
        <li>Executor: {{ event.executor }}</li>
        <li>State: <span :class="{ 'text-green-600': event.state === 'on', 'text-red-600': event.state === 'off' }">{{
            event.state
        }}</span></li>
        <li v-if="event.above">Above: {{ event.above }}</li>
        <li v-if="event.below">Below: {{ event.below }}</li>
        <li v-if="event.equal">Equal: {{ event.equal }}</li>
        <li v-if="event.value">Value: {{ event.value }}</li>
      </ul>
    </div>

  <!--No events message-->
  <div v-else>
    <span class="text-center block text-lg">No events attached</span>
  </div>
</template>
