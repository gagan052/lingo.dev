# Unreal Engine - Lingo.dev CLI

## What is Unreal Engine?

Unreal Engine is a powerful, open-source game engine developed by Epic Games for creating 2D and 3D games, architectural visualizations, and interactive experiences. It features a comprehensive localization system that supports text translation, cultural adaptation, and multi-language content management through its built-in internationalization framework.

## What is Lingo.dev CLI?

Lingo.dev is an AI-powered translation platform. The Lingo.dev CLI reads source files, sends translatable content to large language models, and writes translated files back to your project.

## About this guide

This guide explains how to set up Lingo.dev CLI in an Unreal Engine project. You'll learn how to configure localization files, set up a translation pipeline, and integrate the translations into your game.

## Step 1. Set up a new Unreal Engine project

Create a new Unreal Engine project or open an existing one. For this guide, we'll create a simple project with localizable content.

1. Open Unreal Engine and create a new project
2. Choose a template (e.g., Games > Third Person)
3. Name your project (e.g., "MyLocalizedGame")
4. Enable "Starter Content" if desired
5. Click "Create"

## Step 2. Create source localization content

Unreal Engine uses a localization system based on namespaces, keys, and source strings. Let's create some localizable content.

### Create UI with localizable text

1. Open the Widget Blueprint editor (Content Browser > Add New > User Interface > Widget Blueprint)
2. Create a simple UI with text elements
3. For each text element, set the text property to use localization:
   - In the Details panel, find the Text property
   - Click the dropdown arrow next to the text field
   - Select "Create Binding" > "Bind to Text"
   - In the binding function, use the localization macros

### Create C++ localization data

Create a simple C++ class with localizable text:

```cpp
// MyGameLocalization.h
#pragma once

#include "CoreMinimal.h"

#define LOCTEXT_NAMESPACE "MyGameNamespace"

class MYLOCALIZEDGAME_API FMyGameLocalization
{
public:
    static FText GetWelcomeMessage()
    {
        return NSLOCTEXT("MyGameNamespace", "WelcomeMessage", "Welcome to the game!");
    }
    
    static FText GetStartButtonText()
    {
        return NSLOCTEXT("MyGameNamespace", "StartButton", "Start Game");
    }
    
    static FText GetSettingsText()
    {
        return NSLOCTEXT("MyGameNamespace", "Settings", "Settings");
    }
};

#undef LOCTEXT_NAMESPACE
```

## Step 3. Configure the localization system

### Set up localization paths

1. Open Project Settings (Edit > Project Settings)
2. Navigate to "Engine > Internationalization"
3. Under "Localization Paths", add a new path: `%GAMEDIR%Content/Localization/Game`
4. Click "Add" and set the path to your localization directory

### Create localization target

1. In the Content Browser, navigate to your project's Content folder
2. Create a new folder: `Localization/Game`
3. Right-click in the folder and select "Import to /Game..."
4. This will create the necessary localization structure

## Step 4. Configure the CLI

In the root of your Unreal Engine project, create an `i18n.json` file:

```json
{
  "version": "1.10",
  "locale": {
    "source": "en",
    "targets": [
      "es",
      "fr",
      "de"
    ]
  },
  "buckets": {
    "po": {
      "include": [
        "Content/Localization/Game/[locale]/*.po"
      ]
    }
  },
  "$schema": "https://lingo.dev/schema/i18n.json"
}
```

This file defines:
- the PO files that Lingo.dev CLI should translate
- the languages to translate between

In this case, the configuration translates PO files from English to Spanish, French, and German.

Alternatively, you can create a `lingo.config.js` file for more complex configurations:

```javascript
export default {
  version: "1.10",
  locale: {
    source: "en",
    targets: ["es", "fr", "de"]
  },
  buckets: {
    po: {
      include: ["Content/Localization/Game/[locale]/*.po"]
    }
  },
  $schema: "https://lingo.dev/schema/i18n.json"
};
```

## Step 5. Export localization data

### Use Unreal Engine's localization dashboard

1. Open the Localization Dashboard (Window > Localization Dashboard)
2. Select your localization target (Game)
3. Click "Gather Text" to collect all localizable strings
4. Click "Export Text" to export the localization data as PO files
5. The PO files will be created in `Content/Localization/Game/[culture]/Game.po`

### Manual PO file creation

Alternatively, create a sample PO file manually:

Create `Content/Localization/Game/en/Game.po`:

```po
msgid ""
msgstr ""
"Content-Type: text/plain; charset=UTF-8\n"
"Language: en\n"

msgctxt "MyGameNamespace|WelcomeMessage"
msgid "Welcome to the game!"
msgstr "Welcome to the game!"

msgctxt "MyGameNamespace|StartButton"
msgid "Start Game"
msgstr "Start Game"

msgctxt "MyGameNamespace|Settings"
msgid "Settings"
msgstr "Settings"
```

## Step 6. Translate the content

### Sign up and authenticate

1. Sign up for a Lingo.dev account at https://lingo.dev
2. Log in to Lingo.dev via the CLI:
   ```bash
   npx lingo.dev@latest login
   ```

### Run the translation pipeline

Run the translation pipeline:

```bash
npx lingo.dev@latest run
```

The CLI will:
- Read the English PO files from `Content/Localization/Game/en/`
- Generate translated PO files for each target language
- Create the corresponding directories: `Content/Localization/Game/es/`, `Content/Localization/Game/fr/`, `Content/Localization/Game/de/`
- Create an `i18n.lock` file to track what has been translated

## Step 7. Import translations back to Unreal Engine

### Import translated PO files

1. Return to the Localization Dashboard in Unreal Engine
2. Click "Import Text" to import the translated PO files
3. Select the target cultures (es, fr, de)
4. Click "Import"

### Compile localization data

1. Click "Compile Text" to compile the localization data
2. This creates the `.locres` files that Unreal Engine uses at runtime

## Step 8. Configure game settings for localization

### Set up game localization settings

1. Open Project Settings > Engine > Internationalization
2. Under "Cultures to Cook", add your target languages:
   - es (Spanish)
   - fr (French)
   - de (German)
3. Set the "Default Culture" to "en" (English)

### Configure packaging settings

1. Go to Project Settings > Packaging
2. Under "Advanced", expand "Localization"
3. Set "Localization to Package" to "All"
4. Choose appropriate ICU internationalization data:
   - For European languages: "EFIGS" (English, French, Italian, German, Spanish)
   - For global reach: "All" (~15.3MB)

## Step 9. Use the translations in your game

### In C++ code

Use the localization macros to display translated text:

```cpp
// In your game code
FText WelcomeText = FMyGameLocalization::GetWelcomeMessage();
FText StartButtonText = FMyGameLocalization::GetStartButtonText();
FText SettingsText = FMyGameLocalization::GetSettingsText();

// Display the text in your UI
MyTextBlock->SetText(WelcomeText);
MyButton->SetText(StartButtonText);
```

### In Blueprints

1. Create a Blueprint function to get localized text
2. Use the "Make Literal Text" node with localization keys
3. Bind the text to your UI elements

### Runtime language switching

Implement language switching functionality:

```cpp
// MyGameInstance.h
UFUNCTION(BlueprintCallable, Category = "Localization")
void SetGameLanguage(const FString& LanguageCode);

// MyGameInstance.cpp
void UMyGameInstance::SetGameLanguage(const FString& LanguageCode)
{
    FInternationalization::Get().SetCurrentCulture(LanguageCode);
    
    // Save preference
    UGameUserSettings* Settings = GetGameUserSettings();
    Settings->SetStringSetting(TEXT("Language"), LanguageCode);
    Settings->ApplySettings(false);
}
```

## Step 10. Test the translations

### Test in editor

1. Open the Localization Dashboard
2. Under "Preview", select a target language
3. Play the game in editor to see localized content

### Test packaged build

1. Package your game for the target platform
2. Run the game and test language switching
3. Verify that all text appears correctly in each language

### Test on different platforms

Test your localized game on:
- Windows
- macOS (if supported)
- Console platforms (if applicable)
- Mobile platforms (if applicable)

## Known limitations

1. **PO file format**: Unreal Engine's PO file format includes context information that must be preserved during translation
2. **Culture codes**: Ensure that Unreal Engine's culture codes match Lingo.dev's supported locale codes
3. **Text expansion**: Some languages may require more space than English - design your UI accordingly
4. **Font support**: Ensure your fonts support all target languages and character sets
5. **Right-to-left languages**: Unreal Engine has limited support for RTL languages in UI

## Next steps

To learn more about Unreal Engine's localization system, see:

- [Unreal Engine Localization Documentation](https://dev.epicgames.com/documentation/en-us/unreal-engine/localization-overview-for-unreal-engine)
- [Text Localization Guide](https://dev.epicgames.com/documentation/en-us/unreal-engine/text-localization-in-unreal-engine)
- [Localization Dashboard](https://dev.epicgames.com/documentation/en-us/unreal-engine/localization-dashboard-in-unreal-engine)

For more advanced localization features:
- Implement dynamic text formatting with parameters
- Set up localization for audio and voice-overs
- Configure regional pricing and content variations
- Add support for additional languages and cultures

## Support

For questions and support:
- [Lingo.dev Discord](https://lingo.dev/go/discord)
- Email: support@lingo.dev
- [GitHub Issues](https://github.com/lingodotdev/lingo.dev/issues)