---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: bac2ed447c9055f095e604725591c487378f5091
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399674"
---
## <a name="prerequisites"></a>先决条件

本文假设已有一个 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，可[免费试用语音服务](../../../get-started.md)。

## <a name="install-the-speech-sdk"></a>安装语音 SDK

需要先安装语音 SDK，然后才能执行任何操作。 根据你的平台，按照语音 sdk 文章的<a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">获取语音 sdk <span class="docon docon-navigate-external x-hidden-focus"></span> </a>部分中的说明进行操作。

## <a name="import-dependencies"></a>导入依赖项

若要运行本文中的示例，请在`using` *Program.cs*文件的顶部添加以下语句。

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>敏感数据和环境变量

本文中的示例源代码依赖于用于存储敏感数据的环境变量，如语音资源订阅密钥和区域。 `Program`类包含两个`static readonly string`从主机环境变量分配的值，即`SPEECH__SUBSCRIPTION__KEY`和`SPEECH__SERVICE__REGION`。 这两个字段都在类范围内，使它们可在类的方法体内访问。 有关环境变量的详细信息，请参阅[环境变量和应用程序配置](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)。

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => Task.CompletedTask;
}
```

## <a name="create-a-speech-translation-configuration"></a>创建语音翻译配置

若要使用语音 SDK 调用语音服务，需要创建 [`SpeechTranslationConfig`][config]。 此类包含有关订阅的信息，例如密钥和关联的区域、终结点、主机或授权令牌。

> [!TIP]
> 无论是要执行语音识别、语音合成、翻译还是意向识别，都需要创建一个配置。

可以通过以下几种方法初始化 [`SpeechTranslationConfig`][config]：

* 使用订阅：传入密钥和关联的区域。
* 使用终结点：传入语音服务终结点。 密钥或授权令牌是可选的。
* 使用主机：传入主机地址。 密钥或授权令牌是可选的。
* 使用授权令牌：传入授权令牌和关联的区域。

让我们看看如何使用密钥和区域创建 [`SpeechTranslationConfig`][config]。 请查看[区域支持](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)页以找到你的区域标识符。

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => TranslateSpeechAsync();

    static async Task TranslateSpeechAsync()
    {
        var translationConfig =
            SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>更改源语言

语音转换的一个常见任务是指定输入（或源）语言。 让我们看看如何将输入语言更改为意大利语。 在代码中，与[`SpeechTranslationConfig`][config]实例进行交互，并将分配`SpeechRecognitionLanguage`给属性。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig.SpeechRecognitionLanguage = "it-IT";
}
```

[`SpeechRecognitionLanguage`][recognitionlang] 属性需要语言区域设置格式字符串。 可以提供受支持的[区域设置/语言](../../../language-support.md)的列表中“区域设置”列中的任何值  。

## <a name="add-translation-language"></a>添加翻译语言

语音翻译的另一个常见任务是指定目标翻译语言，至少需要一个语言，但支持多个目标。 在以下代码段中，法语和德语都作为翻译语言目标。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig.SpeechRecognitionLanguage = "it-IT";
    
    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.AddTargetLanguage("fr");
    translationConfig.AddTargetLanguage("de");
}
```

每次调用时[`AddTargetLanguage`][addlang]，都会指定一个新的目标转换语言。 换而言之，当语音被源语言识别出来时，每个目标转换都作为生成的转换操作的一部分提供。

## <a name="initialize-a-translation-recognizer"></a>初始化翻译识别器

创建 [`SpeechTranslationConfig`][config] 后，下一步是初始化 [`TranslationRecognizer`][recognizer]。 初始化 [`TranslationRecognizer`][recognizer] 时，需要向其传递 `translationConfig`。 配置对象提供语音服务验证请求所需的凭据。

如果使用设备的默认麦克风识别语音，则 [`TranslationRecognizer`][recognizer] 应如下所示：

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);
}
```

如果要指定音频输入设备，则需要创建一个 [`AudioConfig`][audioconfig] 并在初始化 [`TranslationRecognizer`][recognizer] 时提供 `audioConfig` 参数。

> [!TIP]
> [了解如何获取音频输入设备的设备 ID](../../../how-to-select-audio-input-devices.md)。

首先，您将引用`AudioConfig`对象，如下所示：

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

如果要提供音频文件而不是使用麦克风，则仍需要提供 `audioConfig`。 但是，在创建 [`AudioConfig`][audioconfig]（而不是调用 `FromDefaultMicrophoneInput`）时，将调用 `FromWavFileInput` 并传递 `filename` 参数。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>翻译语音

为了翻译语音，语音 SDK 依赖于麦克风或音频文件输入。 语音识别发生在语音转换之前。 所有对象初始化完成后，调用识别一次函数并获取结果。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");
    
    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\":");
        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");
        }
    }
}
```

有关语音到文本的详细信息，请参阅[语音识别的基本知识](../../../speech-to-text-basics.md)。

## <a name="synthesize-translations"></a>合成翻译

成功进行语音识别和转换后，结果将包含字典中的所有翻译。 [`Translations`][translations]字典键是目标转换语言，值为已翻译的文本。 可识别的语音可以翻译，然后使用不同的语言（语音到语音）合成。

### <a name="event-based-synthesis"></a>基于事件的合成

`TranslationRecognizer`对象公开`Synthesizing`事件。 事件多次引发，并提供一种机制，用于从转换识别结果中检索合成的音频。 如果要翻译为多种语言，请参阅[手动合成](#manual-synthesis)。 通过分配来指定合成声音， [`VoiceName`][voicename]并为`Synthesizing`事件提供事件处理程序，获取音频。 下面的示例将转换后的音频保存为 *.wav*文件。

> [!IMPORTANT]
> 基于事件的合成仅适用于单个翻译，**不添加多**个目标翻译语言。 此外， [`VoiceName`][voicename]应与目标转换语言相同，例如：`"de"`可以映射到`"de-DE-Hedda"`。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguage = "de";
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    translationConfig.AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.VoiceName = "de-DE-Hedda";

    using var recognizer = new TranslationRecognizer(translationConfig);

    recognizer.Synthesizing += (_, e) =>
    {
        var audio = e.Result.GetAudio();
        Console.WriteLine($"Audio synthesized: {audio.Length:#,0} byte(s) {(audio.Length == 0 ? "(Complete)" : "")}");

        if (audio.Length > 0)
        {
            File.WriteAllBytes("YourAudioFile.wav", audio);
        }
    };

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{toLanguage}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\"");
        Console.WriteLine($"Translated into '{toLanguage}': {result.Translations[toLanguage]}");
    }
}
```

### <a name="manual-synthesis"></a>手动合成

[`Translations`][translations]字典可用于合成转换文本中的音频。 遍历每个翻译，并合成翻译。 创建`SpeechSynthesizer`实例时，需要将`SpeechConfig`对象的[`SpeechSynthesisVoiceName`][speechsynthesisvoicename]属性设置为所需的声音。 下面的示例转换为五种语言，然后将每个翻译合成到相应的神经语言中的音频文件。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        var languageToVoiceMap = new Dictionary<string, string>
        {
            ["de"] = "de-DE-KatjaNeural",
            ["en"] = "en-US-AriaNeural",
            ["it"] = "it-IT-ElsaNeural",
            ["pt"] = "pt-BR-FranciscaNeural",
            ["zh-Hans"] = "zh-CN-XiaoxiaoNeural"
        };

        Console.WriteLine($"Recognized: \"{result.Text}\"");

        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");

            var speechConfig =
                SpeechConfig.FromSubscription(
                    SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);
            speechConfig.SpeechSynthesisVoiceName = languageToVoiceMap[language];

            using var audioConfig = AudioConfig.FromWavFileOutput($"{language}-translation.wav");
            using var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
            
            await synthesizer.SpeakTextAsync(translation);
        }
    }
}
```

有关语音合成的详细信息，请参阅[语音合成的基本知识](../../../text-to-speech-basics.md)。

[config]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig?view=azure-dotnet
[audioconfig]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet
[recognizer]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognizer?view=azure-dotnet
[recognitionlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?view=azure-dotnet
[addlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.addtargetlanguage?view=azure-dotnet
[translations]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognitionresult.translations?view=azure-dotnet
[voicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.voicename?view=azure-dotnet
[speechsynthesisvoicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechsynthesisvoicename?view=azure-dotnet
