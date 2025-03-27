<template>
  <transition name="modal">
    <div v-if="isOpen" class="modal-overlay" @click.self="handleClose">
      <div class="modal">
        <div class="modal__header">
          <button class="modal__header__close" @click="handleClose">
            <img class="modal__header__close-icon" :src="closeIcon" alt="Close" />
          </button>
          <div class="modal__header__txt">
            <h3 class="modal__title">{{ title }}</h3>
            <span class="modal__numberOrder" v-if="orderNumber">№ {{ formattedOrderNumber }}</span>
          </div>
        </div>
        <div class="modal__content">
          <h3 class="modal__question">Расскажите, почему отказались от выполнения заказа?</h3>
          
          <div class="modal__reasons">
            <div 
              v-for="(reason, index) in reasons" 
              :key="index" 
              class="modal__reason"
              :class="{ 'modal__reason--active': selectedReason === index }"
              @click="selectReason(index)"
            >
              <input 
                type="radio" 
                :id="'reason-' + index" 
                :name="'reason'" 
                :checked="selectedReason === index"
                class="modal__reason-input"
              >
              <label :for="'reason-' + index" class="modal__reason-label">{{ reason }}</label>
            </div>
          </div>
          
          <div class="modal__other" v-if="showOtherInput">
            <textarea 
              class="modal__other-input" 
              placeholder="Опишите вашу ситуацию"
              v-model="otherReasonText"
            ></textarea>
          </div>
          
          <button class="modal__submit" @click="submitReasons">Отправить</button>
        </div>
      </div>
    </div>
  </transition>
</template>

<script setup>
import closeIcon from '../../assets/close.svg';
import { computed, onMounted, onBeforeUnmount, ref } from 'vue'

const props = defineProps({
  isOpen: { type: Boolean, required: true },
  onClose: { type: Function, required: true },
  title: { type: String, default: '' },
  content: { type: [String, Object], default: '' },
  orderNumber: { 
    type: [String, Number], 
    default: '',
    validator: (value) => {
      if (typeof value === 'number') return value >= 0;
      return true;
    }
  }
})

const emit = defineEmits(['submit'])

const reasons = ref([
  'Не подходит дата или время',
  'Не подходит тоннаж груза',
  'Не подходит объем груза',
  'Не ПОДХОДИТ ТИП ФУРГОНА',
  'Нет пропуска в МКАД ТТК СК',
  'Поломка машины/авария',
  'Заболел',
  'НЕТ САНОБРАБОТКИ',
  'НЕТ мед. книжки',
  'НЕТ Гидроборта',
  'НЕТ растентовки',
  'Не устраивает ставка за рейс',
  'не устраивает маршрут',
  'Другое'
])

const selectedReason = ref(null)
const showOtherInput = ref(false)
const otherReasonText = ref('')

const selectReason = (index) => {
  selectedReason.value = index
  showOtherInput.value = (index === reasons.value.length - 1) // Показываем поле для "Другое"
}

const submitReasons = () => {
  let reason = ''
  if (selectedReason.value !== null) {
    reason = selectedReason.value === reasons.value.length - 1 
      ? otherReasonText.value 
      : reasons.value[selectedReason.value]
  }
  
  emit('submit', {
    reason,
    orderNumber: props.orderNumber
  })
  
  handleClose()
}

const handleClose = () => {
  props.onClose()
  // Сброс состояния при закрытии
  selectedReason.value = null
  showOtherInput.value = false
  otherReasonText.value = ''
}

const handleEscape = (e) => {
  if (e.key === 'Escape') handleClose()
}

const formattedOrderNumber = computed(() => {
  if (!props.orderNumber) return '';
  return String(props.orderNumber).padStart(8, '0');
});

onMounted(() => window.addEventListener('keydown', handleEscape))
onBeforeUnmount(() => window.removeEventListener('keydown', handleEscape))
</script>

<style scoped lang="scss">
@import './modal.scss';
</style>