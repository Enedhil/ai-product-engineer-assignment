<script setup>
import { ref, computed } from 'vue'

// 1. A feladatból kapott statikus JSON adatok
const data = {
  "campaigns": [
    {
      "id": "camp_001",
      "name": "Welcome Discount Popup",
      "device": "desktop",
      "steps": [
        { "id": "step_1", "name": "Teaser – 10% off", "type": "teaser", "views": 10000, "proceeds": 3200, "description": "Small teaser popup with 'Get 10% off' button." },
        { "id": "step_2", "name": "Email capture", "type": "email", "views": 3200, "proceeds": 850, "description": "Full-screen popup asking for email address." },
        { "id": "step_3", "name": "Success & coupon", "type": "success", "views": 850, "proceeds": 820, "description": "Thank-you screen with coupon code." }
      ]
    },
    {
      "id": "camp_002",
      "name": "Exit-intent Cart Saver",
      "device": "desktop",
      "steps": [
        { "id": "step_1", "name": "Exit intent popup", "type": "exit-intent", "views": 7500, "proceeds": 2100, "description": "Popup triggered on exit intent with 'Wait, here's 15% off' message." },
        { "id": "step_2", "name": "Coupon reveal", "type": "coupon", "views": 2100, "proceeds": 1100, "description": "Shows coupon code and 'Apply discount' button." }
      ]
    },
    {
      "id": "camp_003",
      "name": "Mobile Newsletter Signup",
      "device": "mobile",
      "steps": [
        { "id": "step_1", "name": "Mobile teaser", "type": "teaser", "views": 12000, "proceeds": 1800, "description": "Small bottom bar with 'Join our newsletter' text." },
        { "id": "step_2", "name": "Email + preferences", "type": "email", "views": 1800, "proceeds": 300, "description": "Mobile-friendly form with email and 2 preference checkboxes." },
        { "id": "step_3", "name": "Confirm subscription", "type": "success", "views": 300, "proceeds": 260, "description": "Thank-you screen confirming subscription." }
      ]
    }
  ]
}

// Reaktív változó, ami tárolja, hogy épp melyik kampány van kiválasztva (alapértelmezetten az első)
const selectedCampaignId = ref(data.campaigns[0].id)

// Computed property: megkeresi a kiválasztott kampány objektumot az ID alapján
const selectedCampaign = computed(() => {
  return data.campaigns.find(c => c.id === selectedCampaignId.value)
})

// Függvény a teljes kampány konverziós rátájának kiszámítására (Utolsó lépés proceeds / Első lépés views)
const calculateOverallConversion = (campaign) => {
  if (!campaign.steps.length) return 0
  const firstStepViews = campaign.steps[0].views
  const lastStepProceeds = campaign.steps[campaign.steps.length - 1].proceeds
  return ((lastStepProceeds / firstStepViews) * 100).toFixed(1)
}

// Függvény, ami kiszámolja az egyes lépések közötti lemorzsolódást (drop-off) százalékban
const calculateDropOff = (views, proceeds) => {
  if (views === 0) return 0
  const dropOffCount = views - proceeds
  return ((dropOffCount / views) * 100).toFixed(1)
}

// Computed property: Kiszámolja, hogy a kiválasztott kampányban melyik lépésnél volt a legnagyobb drop-off százalékosan
const worstStepAnalysis = computed(() => {
  const campaign = selectedCampaign.value
  if (!campaign || campaign.steps.length < 2) return null

  let maxDropOffPercent = -1
  let worstStepInfo = null

  // Végigmegyünk a lépéseken, kivéve az utolsót, mert a drop-off mindig a következő lépésre értendő
  for (let i = 0; i < campaign.steps.length; i++) {
    const step = campaign.steps[i]
    const dropPercent = parseFloat(calculateDropOff(step.views, step.proceeds))
    
    if (dropPercent > maxDropOffPercent) {
      maxDropOffPercent = dropPercent
      worstStepInfo = {
        stepName: step.name,
        index: i + 1,
        fromPercent: 100, // Alapértelmezett viszonyítás az adott lépés nézettségéhez
        views: step.views,
        proceeds: step.proceeds,
        dropPercent: dropPercent
      }
    }
  }
  return worstStepInfo
})

// Egyszerű szabályalapú ajánlások generálása (Optional feladat)
const getRecommendations = computed(() => {
  const worst = worstStepAnalysis.value
  if (!worst) return []

  const recommendations = []
  
  if (worst.dropPercent > 50) {
    recommendations.push({
      title: "Kritikus lemorzsolódás",
      text: `A(z) "${worst.stepName}" lépésnél a látogatók több mint fele (${worst.dropPercent}%) eltűnik. Érdemes csökkenteni a beviteli mezők számát vagy egyértelműbb Call-to-Action (CTA) gombot használni.`
    })
  }
  
  if (selectedCampaign.value.device === 'mobile') {
    recommendations.push({
      title: "Mobil optimalizáció",
      text: "Mivel ez egy mobil kampány, győződj meg róla, hogy a billentyűzet felugrása nem takarja el a lényeges gombokat, és a szövegek elég nagyok a kis kijelzőkön is."
    })
  } else {
    recommendations.push({
      title: "Asztali felhasználói élmény",
      text: "Asztali nézetben teszteld az exit-intent (kilépési szándék) időzítését. Ha túl korán ugrik fel a popup, az bosszanthatja a vásárlókat."
    })
  }

  return recommendations
})
</script>

<template>
  <div class="min-h-screen bg-slate-50 text-slate-800 font-sans">
    <header class="bg-white border-b border-slate-200 py-6 px-8 shadow-xs">
      <div class="max-w-7xl mx-auto flex items-center justify-between">
        <div>
          <h1 class="text-2xl font-bold text-indigo-600 tracking-tight">Popup Funnel Analytics</h1>
          <p class="text-sm text-slate-500 mt-1">Junior AI Product Engineer Assignment v1</p>
        </div>
      </div>
    </header>

    <main class="max-w-7xl mx-auto p-6 md:p-8 grid grid-cols-1 lg:grid-cols-3 gap-8">
      
      <section class="lg:col-span-1 space-y-4">
        <h2 class="text-lg font-semibold text-slate-700 px-1">Kampányok kiválasztása</h2>
        <div class="space-y-3">
          <div 
            v-for="campaign in data.campaigns" 
            :key="campaign.id"
            @click="selectedCampaignId = campaign.id"
            :class="[
              'p-5 rounded-xl border transition-all duration-200 cursor-pointer shadow-xs',
              selectedCampaignId === campaign.id 
                ? 'bg-white border-indigo-500 ring-2 ring-indigo-500/10' 
                : 'bg-white border-slate-200 hover:border-slate-300 hover:shadow-md'
            ]"
          >
            <div class="flex justify-between items-start">
              <div>
                <h3 class="font-semibold text-slate-900">{{ campaign.name }}</h3>
                <span class="inline-flex items-center gap-1 mt-1.5 text-xs font-medium text-slate-500 bg-slate-100 px-2 py-0.5 rounded">
                  <span v-if="campaign.device === 'desktop'">💻 Desktop</span>
                  <span v-else>📱 Mobile</span>
                </span>
              </div>
              <div class="text-right">
                <div class="text-sm text-slate-400 font-medium">Összesített konverzió</div>
                <div class="text-xl font-bold text-emerald-600 mt-0.5">{{ calculateOverallConversion(campaign) }}%</div>
              </div>
            </div>
          </div>
        </div>
      </section>

      <section class="lg:col-span-2 space-y-6">
        
        <div class="bg-white rounded-2xl border border-slate-200 p-6 shadow-xs space-y-6">
          <div class="border-b border-slate-100 pb-4">
            <h2 class="text-xl font-bold text-slate-900">{{ selectedCampaign.name }} részletei</h2>
            <p class="text-sm text-slate-500 mt-1">Lépésenkénti tölcsér teljesítmény és lemorzsolódás elemzése.</p>
          </div>

          <div v-if="worstStepAnalysis" class="bg-rose-50 border border-rose-100 rounded-xl p-4 flex items-start gap-3">
            <span class="text-2xl">⚠️</span>
            <div>
              <h4 class="font-bold text-rose-800 text-sm uppercase tracking-wide">Legnagyobb lemorzsolódási pont (Problem Step)</h4>
              <p class="text-rose-900 font-medium mt-1">
                A legnagyobb visszaesés a <span class="font-bold underline">{{ worstStepAnalysis.stepName }}</span> lépésnél történik.
              </p>
              <p class="text-rose-700 text-sm mt-1">
                A felhasználók <span class="font-bold text-rose-800">{{ worstStepAnalysis.dropPercent }}%</span>-a morzsolódik le itt ({{ worstStepAnalysis.views }} megtekintésből csak {{ worstStepAnalysis.proceeds }} lép tovább).
              </p>
            </div>
          </div>

          <div class="space-y-4 pt-2">
            <div v-for="(step, index) in selectedCampaign.steps" :key="step.id" class="relative">
              
              <div 
                :class="[
                  'p-5 rounded-xl border flex flex-col md:flex-row md:items-center justify-between gap-4 relative z-10',
                  worstStepAnalysis && worstStepAnalysis.stepName === step.name 
                    ? 'border-rose-300 bg-rose-50/20 ring-2 ring-rose-500/5' 
                    : 'border-slate-200 bg-white'
                ]"
              >
                <div>
                  <div class="flex items-center gap-2">
                    <span class="bg-slate-900 text-white font-mono text-xs w-5 h-5 rounded-full flex items-center justify-center font-bold">
                      {{ index + 1 }}
                    </span>
                    <h3 class="font-bold text-slate-900 text-base">{{ step.name }}</h3>
                    <span class="text-xs font-mono uppercase px-2 py-0.5 rounded bg-slate-100 text-slate-600 border border-slate-200">
                      {{ step.type }}
                    </span>
                  </div>
                  <p class="text-sm text-slate-500 mt-1 md:max-w-md">{{ step.description }}</p>
                </div>

                <div class="flex items-center gap-6 justify-between md:justify-end border-t md:border-t-0 pt-3 md:pt-0 border-slate-100">
                  <div class="text-center md:text-right">
                    <div class="text-xs text-slate-400 font-medium uppercase tracking-wider">Megtekintés (Views)</div>
                    <div class="text-base font-bold text-slate-800">{{ step.views.toLocaleString() }}</div>
                  </div>
                  <div class="text-center md:text-right">
                    <div class="text-xs text-slate-400 font-medium uppercase tracking-wider">Továbblépett (Proceeds)</div>
                    <div class="text-base font-bold text-indigo-600">{{ step.proceeds.toLocaleString() }}</div>
                  </div>
                </div>
              </div>

              <div 
                v-if="index < selectedCampaign.steps.length - 1" 
                class="flex flex-col items-center my-2 relative z-0"
              >
                <div class="w-0.5 h-6 bg-slate-300"></div>
                <div class="bg-amber-100 border border-amber-200 text-amber-800 font-medium text-xs px-3 py-0.5 rounded-full shadow-xs">
                  🛑 Lemorszsolódás (Drop-off): {{ calculateDropOff(step.views, step.proceeds) }}%
                </div>
                <div class="w-0.5 h-6 bg-slate-300"></div>
              </div>

            </div>
          </div>
        </div>

        <div class="bg-white rounded-2xl border border-slate-200 p-6 shadow-xs space-y-4">
          <div class="flex items-center gap-2 border-b border-slate-100 pb-3">
            <span class="text-xl">💡</span>
            <h3 class="font-bold text-slate-900 text-lg">AI-Asszisztenstől származó javaslatok</h3>
          </div>
          <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
            <div 
              v-for="(rec, idx) in getRecommendations" 
              :key="idx"
              class="bg-indigo-50/40 border border-indigo-100 rounded-xl p-4 space-y-1"
            >
              <h5 class="font-bold text-indigo-900 text-sm">{{ rec.title }}</h5>
              <p class="text-xs text-indigo-950 leading-relaxed">{{ rec.text }}</p>
            </div>
          </div>
        </div>

      </section>
    </main>
  </div>
</template>