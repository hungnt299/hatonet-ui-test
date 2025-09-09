<script setup lang="ts">
import productDetails from "~/data/product";

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

// Preload all images to prevent flashing
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
            <div class="grid grid-cols-[148px_1fr]">
              <div
                class="px-4 font-semibold text-[#231F20] my-auto text-base leading-[150%]"
              >
                Size
              </div>
              <div
                class="py-4 px-8 tracking-[-0.2px] text-[#231F20] text-normal text-base leading-[150%]"
              >
                750 Arc Single Tier Wall Hung Vanity (2 Drawer)
              </div>
            </div>
            <div class="grid grid-cols-[148px_1fr] border-t border-[#0000001A]">
              <div
                class="px-4 font-semibold text-[#231F20] my-auto text-base leading-[150%]"
              >
                Colour
              </div>
              <div
                class="py-4 px-8 tracking-[-0.2px] text-[#231F20] text-normal text-base leading-[150%]"
              >
                Twilight
              </div>
            </div>
            <div class="grid grid-cols-[148px_1fr] border-t border-[#0000001A]">
              <div
                class="px-4 font-semibold text-[#231F20] my-auto text-base leading-[150%]"
              >
                Drawer Front
              </div>
              <div
                class="py-4 px-8 tracking-[-0.2px] text-[#231F20] text-normal text-base leading-[150%]"
              >
                Newport
              </div>
            </div>
            <div class="grid grid-cols-[148px_1fr] border-t border-[#0000001A]">
              <div
                class="px-4 font-semibold text-[#231F20] my-auto text-base leading-[150%]"
              >
                Slabtop
              </div>
              <div
                class="py-4 px-8 tracking-[-0.2px] text-[#231F20] text-normal text-base leading-[150%]"
              >
                Stonecast Slab Top (1200x460x20mm) - Matte White
              </div>
            </div>
            <div class="grid grid-cols-[148px_1fr] border-t border-[#0000001A]">
              <div
                class="px-4 font-semibold text-[#231F20] my-auto text-base leading-[150%]"
              >
                Handles
              </div>
              <div
                class="py-4 px-8 tracking-[-0.2px] text-[#231F20] text-normal text-base leading-[150%]"
              >
                Roma Matte Black 256mm
              </div>
            </div>
          </div>
          <div
            class="py-6 font-semibold text-[#231F20] text-[28px] leading-[125%] tracking-[-1px]"
          >
            $2,419.00
          </div>
          <div>
            <div
              class="text-sm font-normal leading-[150%] text-[#231F20] tracking-[-0.5px]"
            >
              Quantity
            </div>
            <div class="flex items-center gap-3 mt-2">
              <div class="flex border p-2 border-solid border-[#00000024]">
                <button class="flex items-center hover:cursor-pointer">
                  <img
                    src="/images/icons/minus.svg"
                    alt="minus"
                    class="w-6 h-6 min-w-6">
                </button>
                <input
                  type="text"
                  value="1"
                  class="flex justify-center items-center w-[54px] text-center">
                <button class="flex items-center hover:cursor-pointer">
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
  </div>
</template>

<style scoped>
/* Slide right animation */
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

/* Slide left animation */
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

/* Ensure container maintains position during transitions */
.slide-right-enter-active,
.slide-left-enter-active,
.slide-right-leave-active,
.slide-left-leave-active {
  max-height: inherit;
  object-fit: contain;
}
</style>
