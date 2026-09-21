---
id: code-layout-internationalisation
title: Code à ajouter au layout
sidebar_class_name: theme-doc-sidebar-item-hidden
---

```
const { t } = useTranslation();
const [i18nInitialized, setI18nInitialized] = useState(false);

useEffect(() => {
    i18nReady.then(() => setI18nInitialized(true));
}, []);

if (!i18nInitialized) {
    // On pourrait afficher un splash screen ici (expo-splash-screen)
    return null;
}
```