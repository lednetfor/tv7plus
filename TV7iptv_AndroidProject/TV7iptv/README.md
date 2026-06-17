# TV-7iptv — Application Android

Projet Android complet (WebView) qui embarque votre lecteur IPTV Galaxy.
Nom de l'application : **TV-7iptv**
Package : `com.tv7.iptv`

Ce dossier contient TOUT le code source nécessaire. Il faut le **compiler**
pour obtenir le fichier `.apk` installable — je ne peux pas générer le binaire
directement dans cet environnement (pas d'accès au SDK Android / Google Maven).

Voici 3 façons de compiler, classées de la plus simple à la plus technique.

---

## ✅ MÉTHODE 1 — Android Studio (recommandé, gratuit)

1. Installez [Android Studio](https://developer.android.com/studio)
2. `Fichier > Ouvrir` → sélectionnez le dossier `TV7iptv` (ce dossier)
3. Laissez Android Studio télécharger le SDK / Gradle (automatique au premier
   import, nécessite Internet, ~5-10 min)
4. Cliquez sur le menu `Build > Build Bundle(s) / APK(s) > Build APK(s)`
5. Le fichier `.apk` apparaît dans :
   `TV7iptv/app/build/outputs/apk/debug/app-debug.apk`
6. Transférez ce fichier sur votre téléphone Android et installez-le
   (autorisez "Sources inconnues" si demandé).

---

## ✅ MÉTHODE 2 — Service de build en ligne (sans installer quoi que ce soit)

Si vous n'avez pas envie d'installer Android Studio (plusieurs Go), utilisez
un service de build cloud gratuit :

- **GitHub Actions** (gratuit, automatique) :
  1. Créez un dépôt GitHub et uploadez ce dossier `TV7iptv`
  2. Ajoutez le fichier `.github/workflows/build.yml` fourni ci-dessous
  3. GitHub compile automatiquement et vous donne le `.apk` en téléchargement
     dans l'onglet "Actions" → "Artifacts"

- **Appetize.io / Bitrise / Codemagic** : services qui acceptent un projet
  Gradle zippé et renvoient un `.apk` par email/lien.

### Workflow GitHub Actions (à coller dans `.github/workflows/build.yml`)
```yaml
name: Build APK
on: [push, workflow_dispatch]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'
      - name: Grant execute permission for gradlew
        run: chmod +x gradlew
      - name: Build Debug APK
        run: ./gradlew assembleDebug
      - uses: actions/upload-artifact@v4
        with:
          name: TV-7iptv-apk
          path: app/build/outputs/apk/debug/app-debug.apk
```

---

## ✅ MÉTHODE 3 — En ligne de commande (si vous avez déjà le SDK Android)

```bash
cd TV7iptv
chmod +x gradlew
./gradlew assembleDebug
# Résultat : app/build/outputs/apk/debug/app-debug.apk
```

---

## 📁 Structure du projet

```
TV7iptv/
├── build.gradle              (config racine)
├── settings.gradle           (nom du projet : TV-7iptv)
├── gradle.properties
└── app/
    ├── build.gradle           (config module : applicationId, SDK versions)
    ├── proguard-rules.pro
    └── src/main/
        ├── AndroidManifest.xml      (permissions, nom de l'app, activité)
        ├── java/com/tv7/iptv/
        │   └── MainActivity.java    (WebView + télécommande + téléchargements)
        ├── assets/
        │   └── index.html           (← votre lecteur IPTV Galaxy complet)
        └── res/
            ├── values/
            │   ├── strings.xml      (app_name = "TV-7iptv")
            │   ├── colors.xml
            │   └── themes.xml
            ├── mipmap-*/             (icônes générées, thème or/noir)
            └── xml/
                └── network_security_config.xml
```

## ⚠️ Notes importantes

- **Signature** : l'APK généré par `assembleDebug` est signé avec une clé de
  debug automatique. Suffisant pour installer sur votre propre téléphone.
  Pour publier sur le Play Store, il faudra créer une vraie clé de signature
  (`./gradlew assembleRelease` + keystore).
- **minSdk 21** (Android 5.0+) — compatible avec la quasi-totalité des
  appareils en circulation.
- Toutes les fonctionnalités du lecteur HTML (chaînes, ticker live, favoris,
  enregistrement, renommage) fonctionnent telles quelles dans la WebView.
- L'icône de l'application a été générée automatiquement dans le style
  or/noir du player (cercle + triangle play + badge "7").
