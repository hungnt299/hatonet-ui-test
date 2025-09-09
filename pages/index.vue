<script setup lang="ts">
import productDetails from '~/data/product'
const { $toast } = useNuxtApp()

const imageGallery = [
  {
    id: 1,
    parentImage: '/images/image-5.png',
    childImage: '/images/image-1.png'
  },
  {
    id: 2,
    parentImage: '/images/image-6.png',
    childImage: '/images/image-2.png'
  },
  {
    id: 3,
    parentImage: '/images/image-7.png',
    childImage: '/images/image-3.png'
  },
  {
    id: 4,
    parentImage: '/images/image-8.png',
    childImage: '/images/image-4.png'
  }
]

const activeImageIndex = ref(0)
const isAnimating = ref(false)
const animationDirection = ref('right')

const currentImage = computed(() => imageGallery[activeImageIndex.value])

const selectedOptions = ref<Record<number, string>>({})
const isModalOpen = ref(false)
const currentOptionType = ref<(typeof productDetails.optionTypes)[number] | null>(null)
const quantity = ref(1)

onMounted(() => {
  productDetails.optionTypes.forEach(optionType => {
    if (optionType.options && optionType.options.length > 0) {
      selectedOptions.value[optionType.optionTypeId] = optionType.options[0].uid
    }
  })
})

const getSelectedOption = (optionTypeId: number) => {
  const selectedUid = selectedOptions.value[optionTypeId]
  const optionType = productDetails.optionTypes.find(ot => ot.optionTypeId === optionTypeId)
  return optionType?.options.find(opt => opt.uid === selectedUid)
}

const totalPrice = computed(() => {
  let total = 0
  productDetails.optionTypes.forEach(optionType => {
    const selectedOption = getSelectedOption(optionType.optionTypeId)
    if (selectedOption) {
      total += selectedOption.price
    }
  })
  return total * quantity.value
})

const formatPrice = (price: number) => {
  return `$${(price / 100).toLocaleString('en-US', { minimumFractionDigits: 0, maximumFractionDigits: 0 })}`
}

const openOptionModal = (optionType: (typeof productDetails.optionTypes)[number]) => {
  currentOptionType.value = optionType
  isModalOpen.value = true
}

const closeModal = () => {
  isModalOpen.value = false
  currentOptionType.value = null
}

const selectOption = (optionUid: string) => {
  if (currentOptionType.value) {
    selectedOptions.value[currentOptionType.value.optionTypeId] = optionUid
    closeModal()
  }
}

const goToPrevious = () => {
  if (isAnimating.value) return
  animationDirection.value = 'left'
  isAnimating.value = true

  activeImageIndex.value = activeImageIndex.value === 0
    ? imageGallery.length - 1
    : activeImageIndex.value - 1

  setTimeout(() => {
    isAnimating.value = false
  }, 400)
}

const goToNext = () => {
  if (isAnimating.value) return
  animationDirection.value = 'right'
  isAnimating.value = true

  activeImageIndex.value = activeImageIndex.value === imageGallery.length - 1
    ? 0
    : activeImageIndex.value + 1

  setTimeout(() => {
    isAnimating.value = false
  }, 400)
}

const selectImage = (index: number) => {
  if (isAnimating.value || index === activeImageIndex.value) return

  animationDirection.value = index > activeImageIndex.value ? 'right' : 'left'
  isAnimating.value = true
  activeImageIndex.value = index

  setTimeout(() => {
    isAnimating.value = false
  }, 400)
}

const incrementQuantity = () => {
  quantity.value += 1
}

const decrementQuantity = () => {
  if (quantity.value > 1) {
    quantity.value -= 1
  }
}

const updateQuantity = (event: Event) => {
  const target = event.target as HTMLInputElement
  const value = parseInt(target.value) || 1
  quantity.value = Math.max(1, value)
}

const handleQuantityBlur = (event: Event) => {
  const target = event.target as HTMLInputElement
  const value = parseInt(target.value) || 1
  quantity.value = Math.max(1, value)
  target.value = quantity.value.toString()
}

const addToCart = () => {
  if ($toast) {
    $toast.success('The product has been added to your cart')
  }
}

onMounted(() => {
  imageGallery.forEach(image => {
    const img = new Image()
    img.src = image.parentImage
    const childImg = new Image()
    childImg.src = image.childImage
  })
})

console.log(productDetails);
</script>

<template>
  <div class="bg-white max-w-7xl mx-auto">
    <div
      class="flex flex-col lg:flex-row justify-between gap-[22px] py-[120px] px-10"
    >
      <div class="flex flex-col gap-3 max-w-auto lg:max-w-[588px]">
        <div
          class="py-[120px] px-[50px] border border-solid border-[#0000001A] relative overflow-hidden"
        >
          <div class="relative max-h-[346px] min-h-[200px] lg:min-h-[346px] flex items-center justify-center overflow-hidden">
            <Transition
              :name="animationDirection === 'right' ? 'slide-right' : 'slide-left'"
              appear
            >
              <img
                :key="`image-${activeImageIndex}`"
                :src="currentImage.parentImage"
                alt="product main image"
                class="w-auto h-full max-h-[inherit] object-contain"
                loading="eager"
                @load="() => {}"
                @error="() => {}">
            </Transition>
          </div>
          <div class="flex absolute bottom-[11px] right-[10px] gap-1">
            <button
              :disabled="isAnimating"
              class="w-10 h-10 bg-[#EFF2F6CC] rounded-full flex justify-center items-center hover:cursor-pointer transition-opacity duration-200"
              :class="{ 'opacity-50 cursor-not-allowed': isAnimating }"
              @click="goToPrevious"
            >
              <img
                src="/images/icons/arrow-left.svg"
                alt="arrow-left"
                class="h-6 w-6">
            </button>
            <button
              :disabled="isAnimating"
              class="w-10 h-10 bg-[#EFF2F6CC] rounded-full flex justify-center items-center hover:cursor-pointer transition-opacity duration-200"
              :class="{ 'opacity-50 cursor-not-allowed': isAnimating }"
              @click="goToNext"
            >
              <img
                src="/images/icons/arrow-right.svg"
                alt="arrow-right"
                class="h-6 w-6">
            </button>
          </div>
        </div>
        <div class="grid grid-cols-4 gap-3 items-center w-full">
          <div v-for="(image, index) in imageGallery" :key="image.id" class="h-full flex flex-col justify-center" :class="{ 'border-[#231F20] border border-solid': index === activeImageIndex }" @click="selectImage(index)" >
          <img
            :src="image.childImage"
            :alt="`product thumbnail ${index + 1}`"
            class="w-full h-auto hover:cursor-pointer transition-all duration-200"
            :class="{ 'p-[14px]': index === activeImageIndex }"
            >
          </div>
        </div>
      </div>
      <div class="flex flex-col w-full max-w-auto lg:max-w-[510px]">
        <h1
          class="text-5xl font-normal leading-[120%] text-[#231F20] tracking-wide mb-6"
        >
          {{ productDetails.product.name }}
        </h1>
        <p
          class="text-base font-normal leading-[150%] text-[#231F20] tracking-[-0.18px] mb-12"
        >
          {{ productDetails.product.description }}
        </p>
        <div class="w-full">
          <div class="flex items-center gap-4">
            <div
              class="uppercase font-normal leading-[120%] text-xl text-[#231F20] tracking-[1.3px]"
            >
              Choose Options
            </div>
            <div class="flex-1 border-t border-[#231F20]" />
          </div>
          <div class="border border-[#0000001A] mt-6">
            <div
              v-for="(optionType, index) in productDetails.optionTypes"
              :key="optionType.optionTypeId"
              class="grid grid-cols-[148px_1fr]"
              :class="{ 'border-t border-[#0000001A]': index > 0 }"
            >
              <div
                class="px-4 font-semibold text-[#231F20] my-auto text-base leading-[150%]"
              >
                {{ optionType.displayName }}
              </div>
              <div
                class="py-4 px-1 lg:px-8 tracking-[-0.2px] text-[#231F20] text-normal text-base leading-[150%] cursor-pointer hover:bg-gray-50 transition-colors duration-200"
                @click="openOptionModal(optionType)"
              >
                {{ getSelectedOption(optionType.optionTypeId)?.displayName || 'Select option' }}
              </div>
            </div>
          </div>
          <div
            class="py-6 font-semibold text-[#231F20] text-[28px] leading-[125%] tracking-[-1px]"
          >
            {{ formatPrice(totalPrice) }}
          </div>
          <div>
            <div
              class="text-sm font-normal leading-[150%] text-[#231F20] tracking-[-0.5px]"
            >
              Quantity
            </div>
            <div class="flex items-center gap-3 mt-2">
              <div class="flex border p-2 border-solid border-[#00000024]">
                <button
                  class="flex items-center hover:cursor-pointer"
                  @click="decrementQuantity"
                >
                  <img
                    src="/images/icons/minus.svg"
                    alt="minus"
                    class="w-6 h-6 min-w-6">
                </button>
                <input
                  type="number"
                  :value="quantity"
                  min="1"
                  class="flex justify-center items-center w-[54px] text-center border-none outline-none"
                  @input="updateQuantity"
                  @blur="handleQuantityBlur">
                <button
                  class="flex items-center hover:cursor-pointer"
                  @click="incrementQuantity"
                >
                  <img
                    src="/images/icons/plus.svg"
                    alt="plus"
                    width="24"
                    height="24"
                    class="w-6 h-6 min-w-6">
                </button>
              </div>
              <button
                class="w-full flex gap-2 h-[44px] justify-center items-center bg-[#231F20] hover:cursor-pointer"
                @click="addToCart"
              >
                <img
                  src="/images/icons/basket-add.svg"
                  alt="add"
                  class="w-6 h-6">
                <span
                  class="text-white text-semibold text-sm leading-6 uppercase"
                  >Add to Cart</span
                >
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>

    <Transition
      enter-active-class="transition-opacity duration-400"
      enter-from-class="opacity-0"
      enter-to-class="opacity-100"
      leave-active-class="transition-opacity duration-400"
      leave-from-class="opacity-100"
      leave-to-class="opacity-0"
    >
      <div
        v-if="isModalOpen"
        class="fixed inset-0 flex z-50"
        style="background-color: #00000040"
        @click="closeModal"
      >
        <Transition
          enter-active-class="transition-transform duration-400 ease-out"
          enter-from-class="translate-x-full"
          enter-to-class="translate-x-0"
          leave-active-class="transition-transform duration-400 ease-in"
          leave-from-class="translate-x-0"
          leave-to-class="translate-x-full"
        >
          <div
            v-if="isModalOpen"
            class="bg-white w-[512px] h-full ml-auto flex flex-col"
            @click.stop
          >
        <div class="flex justify-between items-center p-8 h-[88px] shadow-[0px_0px_16px_0px_#00000014]">
          <h3 class="text-xl font-normal text-[#231F20] leading-[120%] tracking-[1px] uppercase">
            {{ currentOptionType?.displayName }}
          </h3>
          <button
            class="text-gray-400 hover:text-gray-600 transition-colors duration-200"
            @click="closeModal"
          >
          <button class="hover:cursor-pointer">
            <img src="/images/icons/x.png" alt="close" class="w-6 h-6">
          </button>
          </button>
        </div>

        <div class="flex-1 overflow-y-auto">
          <div class="px-4 py-4">
            <div class="space-y-0">
              <div
                v-for="(option, index) in currentOptionType?.options"
                :key="option.uid"
                class="flex items-center justify-between py-4 px-4 cursor-pointer hover:bg-gray-50 transition-colors duration-200"
                :class="{ 'border-b border-[#0000001A]': index < (currentOptionType?.options?.length || 0) - 1 }"
                @click="selectOption(option.uid)"
              >
                <div class="flex-1">
                  <div class="font-semibold text-[#231F20] text-sm leading-[150%] tracking-[-0.5px]">
                    {{ option.displayName }}
                  </div>
                </div>
                <div class="flex items-center">
                  <div
                    v-if="currentOptionType && selectedOptions[currentOptionType.optionTypeId] === option.uid"
                    class="w-5 h-5 bg-[#231F20] rounded-full flex items-center justify-center"
                  >
                    <svg class="w-3 h-3 text-white" fill="currentColor" viewBox="0 0 20 20">
                      <path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd" />
                    </svg>
                  </div>
                  <div
                    v-else
                    class="w-5 h-5 border-2 border-gray-300 rounded-full"
                  />
                </div>
              </div>
            </div>
          </div>
        </div>
          </div>
        </Transition>
      </div>
    </Transition>
  </div>
</template>

<style scoped>
.slide-right-enter-active {
  transition: transform 0.4s cubic-bezier(0.25, 0.46, 0.45, 0.94);
}

.slide-right-leave-active {
  transition: transform 0.4s cubic-bezier(0.55, 0.06, 0.68, 0.19);
  position: absolute;
  width: 100%;
}

.slide-right-enter-from {
  transform: translateX(100%);
}

.slide-right-leave-to {
  transform: translateX(-100%);
}

.slide-left-enter-active {
  transition: transform 0.4s cubic-bezier(0.25, 0.46, 0.45, 0.94);
}

.slide-left-leave-active {
  transition: transform 0.4s cubic-bezier(0.55, 0.06, 0.68, 0.19);
  position: absolute;
  width: 100%;
}

.slide-left-enter-from {
  transform: translateX(-100%);
}

.slide-left-leave-to {
  transform: translateX(100%);
}

.slide-right-enter-active,
.slide-left-enter-active,
.slide-right-leave-active,
.slide-left-leave-active {
  max-height: inherit;
  object-fit: contain;
}
</style>
