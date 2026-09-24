---
id: code-index-internationalisation
title: Code de la page index.ts
sidebar_class_name: theme-doc-sidebar-item-hidden
---

```
import AsyncStorage from "@react-native-async-storage/async-storage";
import * as Localization from "expo-localization";
import i18n from "i18next";
import { initReactI18next } from "react-i18next";

import en from "./locales/en.json";
import fr from "./locales/fr.json";

const LANGUAGE_STORAGE_KEY = "APP_LANGUAGE";

const resources = {
  fr: { translation: fr },
  en: { translation: en },
};

// Langues supportées par l'app. Ajouter une entrée ici + un fichier
// dans locales/ + une ressource ci-dessus pour ajouter une langue.
export const SUPPORTED_LANGUAGES = ["fr", "en"] as const;
export type SupportedLanguage = (typeof SUPPORTED_LANGUAGES)[number];

async function resolveInitialLanguage(): Promise<SupportedLanguage> {
  try {
    const saved = await AsyncStorage.getItem(LANGUAGE_STORAGE_KEY);
    if (saved && (SUPPORTED_LANGUAGES as readonly string[]).includes(saved)) {
      return saved as SupportedLanguage;
    }
  } catch (error) {
    console.log("Erreur de lecture de la langue sauvegardée :", error);
  }

  // Pas de langue sauvegardée : on se base sur la langue de l'appareil.
  const deviceLanguage = Localization.getLocales()[0]?.languageCode;
  if (
    deviceLanguage &&
    (SUPPORTED_LANGUAGES as readonly string[]).includes(deviceLanguage)
  ) {
    return deviceLanguage as SupportedLanguage;
  }

  return "fr";
}

export const i18nReady = (async () => {
  const initialLanguage = await resolveInitialLanguage();

  await i18n.use(initReactI18next).init({
    compatibilityJSON: "v4",
    resources,
    lng: initialLanguage,
    fallbackLng: "fr",
    interpolation: {
      escapeValue: false, // React échappe déjà les valeurs
    },
  });
})();

export async function changeLanguage(lang: SupportedLanguage) {
  await i18n.changeLanguage(lang);
  try {
    await AsyncStorage.setItem(LANGUAGE_STORAGE_KEY, lang);
  } catch (error) {
    console.error("Erreur de sauvegarde de la langue :", error);
  }
}

export default i18n;
```
