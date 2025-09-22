<template>
  <StaticLayout title="Gerätelisten formatieren">
    <div class="inner-content">
      <div class="left">
        <select v-model="mode">
          <option value="volte">VoLTE Liste</option>
          <option value="wifi">WiFi Liste</option>
        </select>
        <textarea
            placeholder="Liste kopieren"
            v-model="rawInput"
            rows="30"
        />
      </div>
      <div @click="copy(formattedOutput)" v-if="formattedOutput.length" class="right">
        <pre>{{ formattedOutput }}</pre>
      </div>
    </div>
  </StaticLayout>
</template>

<script setup lang="ts">
import { ref, computed } from "vue";
import StaticLayout from "@/components/layouts/StaticLayout.vue";

const rawInput = ref<string>("");

// Parser-Modus: volte oder wifi
const mode = ref<"volte" | "wifi">("volte");

// Hersteller-Mapping
const manufacturers: Record<string, string> = {
  Apple: "apple",
  Samsung: "samsung",
  Xiaomi: "xiaomi",
  HUAWEI: "huawei",
  OnePlus: "oneplus",
  OPPO: "oppo",
  realme: "realme",
  ZTE: "zte",
  vivo: "vivo",
  "Nokia/HMD": "nokia",
  Motorola: "motorola",
  Gigaset: "gigaset",
  Google: "google",
  emporia: "emporia",
  Nothing: "nothing",
  Fairphone: "fairphone",
  Sony: "sony",
  Xplora: "xplora",
  Nubia: "nubia",
};

// Länder-Abschnitte erkennen
const countryKeys = {
  "spusu AT VoLTE": "AT",
  "spusu IT VoLTE": "IT",
  "spusu UK VoLTE": "UK",
  "spusu CH VoLTE": "CH",
  "spusu AT VoWiFi": "AT",
  "spusu IT VoWiFi": "IT",
  "spusu UK VoWiFi": "UK",
  "spusu CH VoWiFi": "CH",
};

// Hilfsfunktion: Normalisieren
function normalizeDeviceName(name: string): string {
  return name
      .replace(/\s+/g, " ") // doppelte Leerzeichen weg
      .replace(/Ultra/gi, "Ultra") // Ultra einheitlich
      .replace(/Pro\s+/gi, "Pro ") // Pro spacing fix
      .replace(/\b5g\b/gi, "5G") // 5g -> 5G
      .trim();
}

const parsed = computed(() => {
  const result: Record<string, Record<string, Set<string>>> = {};
  let currentCountry: string | null = null;
  let currentBrand: string | null = null;

  if (!rawInput.value) return {};

  rawInput.value.split("\n").forEach((line) => {
    line = line.trim();
    if (!line) return;

    // 1. Land erkennen
    for (const [label, code] of Object.entries(countryKeys)) {
      if (line.includes(label)) {
        currentCountry = code;
        if (!result[currentCountry]) {
          result[currentCountry] = {};
          for (const value of Object.values(manufacturers)) {
            result[currentCountry][value] = new Set();
          }
        }
        currentBrand = null;
        return;
      }
    }

    // 2. Hersteller erkennen
    for (const [key, value] of Object.entries(manufacturers)) {
      const cleanLine = line
          .replace(/^-?\d+(\.\d+)*\)?\s*/g, "")
          .replace(/\s*\(.*?\)$/, "")
          .trim();

      if (cleanLine === key) {
        currentBrand = value;
        return;
      }
    }

    // 3. Gerät hinzufügen
    if (currentCountry && currentBrand) {
      const clean = line.replace(/^-?\d+(\.\d+)*\)?\s*/g, "").trim();
      if (clean && clean !== "-") {
        result[currentCountry][currentBrand].add(normalizeDeviceName(clean));
      }
    }
  });

  // Sets zurück in Arrays umwandeln
  const finalResult: Record<string, Record<string, string[]>> = {};
  for (const [country, brands] of Object.entries(result)) {
    finalResult[country] = {};
    for (const [brand, devices] of Object.entries(brands)) {
      finalResult[country][brand] = Array.from(devices).sort();
    }
  }

  return finalResult;
});

const formattedOutput = computed(() => {
  return Object.entries(parsed.value)
      .map(
          ([country, devices]) =>
              `export const ${mode.value}Devices${country} = ${JSON.stringify(
                  devices,
                  null,
                  2
              )};`
      )
      .join("\n\n");
});

async function copy(text: string) {
  try {
    await navigator.clipboard.writeText(text);
    alert("Geräteliste kopiert ✅");
  } catch (err) {
    console.error("Fehler beim Kopieren:", err);
  }
}
</script>


<style scoped>
.inner-content {
  display: flex;
  flex-direction: column;
  gap: 16px;

  .left {
    display: flex;
    flex-direction: column;
    gap: 16px;

    select {
      height: 50px;
      background: none;
      border: 2px solid black;
      font-size: 18px;
      cursor: pointer;
      padding: 0 16px;


      option {
        font-size: 18px;
        cursor: pointer;
      }
    }

    textarea {
      width: 100%;
      padding: 16px;

      &::placeholder {
        font-family: SuseMono, sans-serif;
      }
    }
  }

  .right {
    display: flex;
    flex-direction: column;
    gap: 16px;
    border: 2px dotted black;
    cursor: pointer;
    padding: 16px;
  }
}

@media (min-width: 1200px) {
  .inner-content {
    flex-direction: row;
    gap: 32px;

    .left, .right {
      flex: 1;
    }
  }
}

</style>
