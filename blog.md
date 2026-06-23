# Expressing Japanese Grammar Through TypeScript Type System

Despite one being used for human communication and the other for machine instructions, natural languages and programming languages share similar fundamental principles. Both are defined by grammatical rules, structural constraints, and methods of combination.

For example, Japanese is known for its rigorous grammatical rules and rich conjugation system, while TypeScript's type system is valued for its expressiveness and flexibility. What happens when we combine these two domains? This article will explore how to use TypeScript's advanced generic programming to model Japanese grammatical structures, making programming languages a new tool for language learning.

Let's start with the basics and progress step by step.

## From Imperative Programming to Type Programming

Before diving into Japanese grammar, we can first understand how to convert a regular JavaScript function to a TypeScript type-level implementation through a simple English verb conjugation example.

First, here's a simple JavaScript function that adds either "ed" or "ing" to an English verb based on the second parameter:

```javascript
function conjugateEnglishVerb(verb, form) {
  if (form === "past") {
    return verb + "ed";
  } else if (form === "progressive") {
    return verb + "ing";
  } else {
    return verb;
  }
}

// Usage examples
conjugateEnglishVerb("walk", "past"); // "walked"
conjugateEnglishVerb("talk", "progressive"); // "talking"
```

This function receives parameters at runtime and returns results. This is a typical imperative programming approach, telling the computer "how" to perform a task through a series of explicit instructions. Now, let's convert this logic to TypeScript's type system:

```typescript
// Define possible conjugation forms
type EnglishVerbForm = "base" | "past" | "progressive";

// Type-level verb conjugation function
type ConjugateEnglishVerb<
  Verb extends string,
  Form extends EnglishVerbForm
> = Form extends "past"
  ? `${Verb}ed`
  : Form extends "progressive"
  ? `${Verb}ing`
  : Verb;

// Type-level usage examples
type WalkedVerb = ConjugateEnglishVerb<"walk", "past">; // result: "walked"
type TalkingVerb = ConjugateEnglishVerb<"talk", "progressive">; // result: "talking"
```

Note the key transformations here:

1. Function parameters become generic parameters `<Verb extends string, Form extends EnglishVerbForm>`
2. `if/else` conditional statements become conditional types `Form extends "past" ? ... : ...`
3. String concatenation `verb + "ed"` becomes template string type `` `${Verb}ed` ``
4. Function calls become type declarations and instantiations

This simple example uses three core tools in TypeScript's type system:

- **Generics**: For parameterizing types
- **Conditional types**: For selecting different types based on conditions
- **Template string types**: For manipulating strings at the type level

Comparing these two examples, we can feel that declarative type programming differs fundamentally from imperative programming in how we communicate with computers:

- Imperative programming tells the computer "how to do" something through a series of explicit steps.
- Type programming describes "what something is" by declaring relationships and constraints between types. Instead of issuing sequences of instructions, we build a network of type relationships and let the compiler derive results at compile time.

This paradigm shift allows us to validate the correctness of complex rules before program execution, transforming runtime errors into compile-time errors, which is very suitable for modeling human grammar—a system with strict structural constraints (at least on paper).

## Extending Grammatical Rule Exceptions

Of course, actual language rules are much more complex than the simple transformations above, with many manually defined exceptions. Let's extend our English verb conjugation example to consider some special rules:

```typescript
// More complex English verb conjugation
type ConjugateEnglishVerbAdvanced<
  Verb extends string,
  Form extends EnglishVerbForm
> = Form extends "past"
  ? Verb extends `${infer Base}e`
    ? `${Base}ed` // If ending with e, just add d
    : Verb extends `${infer Base}y`
    ? `${Base}ied` // If ending with y, change y to ied
    : `${Verb}ed`
  : Form extends "progressive"
  ? Verb extends `${infer Base}e`
    ? `${Base}ing` // If ending with e, remove e then add ing
    : `${Verb}ing`
  : Verb;

// Examples
type MovedVerb = ConjugateEnglishVerbAdvanced<"move", "past">; // "moved", not "moveed"
type CriedVerb = ConjugateEnglishVerbAdvanced<"cry", "past">; // "cried", not "cryed"
type MovingVerb = ConjugateEnglishVerbAdvanced<"move", "progressive">; // "moving", not "moveing"
```

Here we use the `infer` keyword to extract specific segments from type expressions (so-called "patterns") and reuse them in the result. This is a powerful tool in the type system called pattern matching.

## Basics of Japanese Verb Conjugation: Understanding Godan Verbs

For readers unfamiliar with Japanese, let's briefly introduce the basic concept of Japanese verb conjugation.

In Japanese, verbs change form based on tense, mood, politeness level, and other factors. This is similar to "walk/walked/walking" in English, but the Japanese conjugation system is more systematic and rule-rich.

Japanese verbs are mainly divided into three categories: godan verbs, ichidan verbs, and irregular verbs. Among them, godan verbs are the most common and have the most complex conjugation rules. The term "godan" comes from the vowel row changes in traditional Japanese grammar (the five rows あ, い, う, え, お), meaning that the verb endings change across these five rows during conjugation.

Taking the godan verb "買う" (to buy) as an example, its changes in different forms are as follows:

- Basic form (dictionary form): 買う (_kau_)
- Te-form (connective form): 買って (_katte_)
- Ta-form (past tense): 買った (_katta_)
- Nai-form (negative form): 買わない (_kawanai_)
- Imperative form: 買え (_kae_)

If we were to implement this conjugation logic with a JavaScript function, it would look something like this:

```javascript
function conjugateGodanVerb(stem, ending, form) {
  if (form === "て形") {
    if (ending === "う" || ending === "つ" || ending === "る") {
      return stem + "って";
    } else if (ending === "く") {
      return stem + "いて";
    } else if (ending === "ぐ") {
      return stem + "いで";
    } else if (ending === "す") {
      return stem + "して";
    } else if (ending === "ぬ" || ending === "ぶ" || ending === "む") {
      return stem + "んで";
    }
  } else if (form === "た形") {
    if (ending === "う" || ending === "つ" || ending === "る") {
      return stem + "った";
    } else if (ending === "く") {
      return stem + "いた";
    } else if (ending === "ぐ") {
      return stem + "いだ";
    } else if (ending === "す") {
      return stem + "した";
    } else if (ending === "ぬ" || ending === "ぶ" || ending === "む") {
      return stem + "んだ";
    }
  }
  // Other form conjugations...
  return stem + ending; // Default returns dictionary form
}

// Usage examples
console.log(conjugateGodanVerb("買", "う", "て形")); // "買って"
console.log(conjugateGodanVerb("書", "く", "た形")); // "書いた"
```

This function demonstrates the basic logic of godan verb conjugation: selecting the appropriate conjugation rule based on the ending (`ending`) and target form (`form`). While it can indeed handle godan verb conjugations, this imperative `if-else` implementation has some obvious drawbacks:

- First, it can only check and handle errors at runtime, providing no guarantee of grammatical correctness at the compilation stage.
- Second, this type of function is difficult to integrate with larger language structure systems, lacking composability.
- Finally, as rules increase, nested conditional judgments lead to a dramatic increase in code complexity, making maintenance difficult.

In contrast, TypeScript's type system provides a declarative method that can catch errors at compile time and describe relationships between language rules in a more elegant and expressive way. By elevating these conjugation rules to the type level, we can build a system that statically validates Japanese grammatical correctness, which is exactly the method we will demonstrate below.

## Implementing Japanese Verb Conjugation with Type Programming

With the above groundwork, we can now start building a type system for Japanese verb conjugation. Let's start with a simple example:

```typescript
// Define simplified Japanese verb types
type SimpleJapaneseVerb = {
  type: "godan" | "ichidan";
  stem: string;
  ending: string;
};

// Define conjugation forms
type JapaneseVerbForm = "辞書形" | "て形" | "た形";

// Simplified Japanese verb conjugation
type ConjugateSimpleJapaneseVerb<
  V extends SimpleJapaneseVerb,
  Form extends JapaneseVerbForm
> = Form extends "辞書形"
  ? `${V["stem"]}${V["ending"]}`
  : Form extends "て形"
  ? V["type"] extends "godan"
    ? V["ending"] extends "う"
      ? `${V["stem"]}って`
      : V["ending"] extends "く"
      ? `${V["stem"]}いて`
      : `${V["stem"]}${V["ending"]}`
    : V["type"] extends "ichidan"
    ? `${V["stem"]}て`
    : `${V["stem"]}${V["ending"]}`
  : Form extends "た形"
  ? V["type"] extends "godan"
    ? V["ending"] extends "う"
      ? `${V["stem"]}った`
      : V["ending"] extends "く"
      ? `${V["stem"]}いた`
      : `${V["stem"]}${V["ending"]}`
    : V["type"] extends "ichidan"
    ? `${V["stem"]}た`
    : `${V["stem"]}${V["ending"]}`
  : `${V["stem"]}${V["ending"]}`;

// Examples
type KauVerb = { type: "godan"; stem: "買"; ending: "う" };
type KauTeForm = ConjugateSimpleJapaneseVerb<KauVerb, "て形">; // "買って"
type KauTaForm = ConjugateSimpleJapaneseVerb<KauVerb, "た形">; // "買った"

type TaberuVerb = { type: "ichidan"; stem: "食べ"; ending: "る" };
type TaberuTeForm = ConjugateSimpleJapaneseVerb<TaberuVerb, "て形">; // "食べて"
type TaberuTaForm = ConjugateSimpleJapaneseVerb<TaberuVerb, "た形">; // "食べた"
```

Now, we can extend this model to more accurately reflect the classification and conjugation rules of Japanese verbs. We'll use interface inheritance to represent different types of verbs and their characteristics:

```typescript
// Base verb interface
interface Verb {
  dictionary: string;
}

// Godan verbs
interface GodanVerb extends Verb {
  stem: string;
  ending: "う" | "く" | "ぐ" | "す" | "つ" | "ぬ" | "ぶ" | "む" | "る";
}

// Ichidan verbs
interface IchidanVerb extends Verb {
  stem: string;
  ending: "る";
}

// Irregular verbs
interface IrregularVerb extends Verb {
  dictionary: "する" | "来る";
}

// Define conjugation forms
type ConjugationForm = "て形" | "た形" | "ない形" | "辞書形" | "命令形";

// More complete Japanese verb conjugation system
type ConjugateVerb<
  T extends Verb,
  Form extends ConjugationForm
> = T extends GodanVerb
  ? GodanConjugation<T, Form>
  : T extends IchidanVerb
  ? IchidanConjugation<T, Form>
  : T extends IrregularVerb
  ? IrregularConjugation<T, Form>
  : never;

// Examples
type 買う = GodanVerb & { stem: "買"; ending: "う" };
type 買うて形 = ConjugateVerb<買う, "て形">; // 買って
type 買うた形 = ConjugateVerb<買う, "た形">; // 買った

type 食べる = IchidanVerb & { stem: "食べ"; ending: "る" };
type 食べるて形 = ConjugateVerb<食べる, "て形">; // 食べて
type 食べるた形 = ConjugateVerb<食べる, "た形">; // 食べた
```

This approach demonstrates the powerful expressiveness of TypeScript's type system. By using generics, conditional types, and template string types, we can validate and transform Japanese verbs at compile time, just like using functions at runtime.

In the following sections, we will continue to use these basic capabilities to simulate more complex Japanese sentence structures, such as interrogative and conditional sentences.

## Interrogative Sentence Type

Japanese interrogative sentences are typically formed using interrogative words (like なんで, なぜ, どうして) and sentence-final interrogative particles (like の, か). Looking at Ms. Nagasaki Soyo's famous quote "なんで春日影やったの" (Why play Haruhikage?), we can identify its structural components:

1. なんで (why) - interrogative adverb
2. 春日影 (Haruhikage) - the object of the sentence
3. やった (played) - past tense form of the verb "やる"
4. の - interrogative particle

This structure is very common in Japanese, and we can abstract a general pattern: `[interrogative word] + [topic/object] + [verb conjugation] + [interrogative particle]`. To represent this pattern in the type system, we need a dedicated generic type `InterrogativePhrase`.

```typescript
// Interrogative words categorized
type WhyInterrogative = "なぜ" | "なんで" | "どうして";
type WhenInterrogative = "いつ";
type WhereInterrogative = "どこ";
type WhoInterrogative = "だれ" | "誰";
type WhatInterrogative = "何" | "なに";
type HowInterrogative = "どう" | "どうして";
type WhatKindInterrogative = "どんな";
type WhichInterrogative = "どれ";

// Interrogative adverb type
type InterrogativeAdverb =
  | WhyInterrogative
  | WhenInterrogative
  | WhereInterrogative
  | WhoInterrogative
  | WhatInterrogative
  | HowInterrogative
  | WhatKindInterrogative
  | WhichInterrogative;

type InterrogativePhrase<
  Adv extends InterrogativeAdverb,
  Subject extends string,
  V extends Verb,
  VForm extends ConjugationForm,
  QP extends Particle = "か" // Interrogative sentences typically default to か as the particle, can be overwritten
> = `${Adv}${Subject}${ConjugateVerb<V, VForm>}${QP}`;
```

This generic type can take an interrogative adverb, topic (or object), verb, verb form, and interrogative particle as parameters, then combine them according to Japanese grammar rules into a complete interrogative sentence type.

Now, let's see how to use this type system to represent the sentence "なんで春日影やったの" (Why play Haruhikage?):

```typescript
// Define the verb "やる" (do/play)
type やる = GodanVerb & { stem: "や"; ending: "る" };

// Define the proper noun "春日影"
type 春日影 = ProperNoun<"春日影">;

// Build the complete interrogative sentence
type なんで春日影やったの = InterrogativePhrase<
  WhyInterrogative, // Interrogative word "なんで" (why)
  春日影, // Object "春日影"
  やる, // Verb "やる"
  "た形", // Verb appears in past tense form
  "の" // Interrogative particle "の"
>;

// Type checking example
const validQuestion: なんで春日影やったの = "なんで春日影やったの"; // "Why play Haruhikage?"
```

In this way, TypeScript's type system can not only represent the structure of Japanese interrogative sentences but also validate the grammatical correctness of sentences at compile time. This demonstrates how the type system can help us learn and master Japanese grammar rules.

## Conditional Sentence Type

Japanese conditional sentences express hypothetical or conditional relationships and have multiple expressions such as なら, たら, れば, and と. Among these conditional expressions, "なら" is commonly used to express "if it is...", typically following a noun or noun phrase.

Let's analyze Ms. Frieren's famous quote "ヒンメルならそうした" (Himmel would have done the same):

1. ヒンメル - the name "Himmel", serving as the subject of the condition
2. なら - conditional particle, expressing "if it is..."
3. そう - demonstrative adverb, meaning "that way"
4. した - past tense of the verb "する" (to do)

The structure of this sentence can be summarized as: `[condition subject] + [conditional particle なら] + [result]`, where the result part is "そうした" (did that way).

To represent this structure in the type system, we need to use several key types defined earlier:

```typescript
// Conditional particle type
type ConditionalParticle = "なら" | "たら" | "れば" | "と";

// Demonstrative type (needs additional definition)
type Demonstrative = "こう" | "そう" | "ああ" | "どう";

// Conditional sentence structure
type ConditionalPhrase<
  Subject extends string,
  CP extends ConditionalParticle,
  Result extends string
> = `${Subject}${CP}${Result}`;

// Demonstrative action combination
type DemonstrativeAction<
  Demo extends string,
  V extends Verb,
  F extends ConjugationForm = "辞書形"
> = `${Demo}${ConjugateVerb<V, F>}`;
```

Now, let's use these types to represent "ヒンメルならそうした" (Himmel would have done the same):

```typescript
// Define the proper noun "ヒンメル"
type ヒンメル = ProperNoun<"ヒンメル">;

// Define the verb する
type する = IrregularVerb & { dictionary: "する" };

// Create the そうした pattern (past tense of そうする)
type そうした = DemonstrativeAction<Demonstrative & "そう", する, "た形">;

// Create the conditional sentence "ヒンメルならそうした"
type ヒンメルならそうした = ConditionalPhrase<ヒンメル, "なら", そうした>;

// Type checking example
const properExample: ヒンメルならそうした = "ヒンメルならそうした"; // "Himmel would have done the same"
// const wrongExample: ヒンメルならそうした = "ヒンメルならそうする"; // Error: verb form doesn't match
```

This example demonstrates how to use the type system to precisely represent Japanese conditional sentence structures and check the grammatical correctness of sentences at compile time.

## Compound Sentence Type Gymnastics: Good Times, Come On!

A common expression in Japanese is connecting multiple short sentences with commas to form a rhythmic compound sentence. These short sentences are independent of each other but together convey a coherent meaning. Let's analyze Mr. Tadokoro Koji's famous quote "いいよ、来いよ" (Good times, come on!), which consists of three parts:

1. いいよ - composed of the adjective "いい" (good) and the emphatic particle "よ"
2. Japanese comma, used to separate short sentences
3. 来いよ - composed of the imperative form "来い" of the verb "来る" (to come) and the emphatic particle "よ"

This sentence structure can be summarized as: `[short sentence 1]、[short sentence 2]`, where each short sentence can have different grammatical structures.

To represent this compound sentence structure in the type system, we can use the following types:

```typescript
// Phrase with particle
type PhraseWithParticle<
  Phrase extends string,
  P extends Particle
> = `${Phrase}${P}`;

// Phrases connected by Japanese comma
type ConnectedPhrases<P1 extends string, P2 extends string> = `${P1}、${P2}`;
```

And for adjective and verb conjugations, we can use the conjugation systems defined earlier:

```typescript
// Define the i-adjective "いい" (good), note that it has irregular conjugation
type いい = IAdjective & { stem: "い"; ending: "い"; irregular: true };
type いいよ = PhraseWithParticle<ConjugateAdjective<いい, "基本形">, "よ">;

// Define the irregular verb "来る" (to come)
type 来る = IrregularVerb & { dictionary: "来る" };
type 来いよ = PhraseWithParticle<ConjugateVerb<来る, "命令形">, "よ">;

// Connect the two short sentences -> "いいよ、来いよ"
type いいよ来いよ = ConnectedPhrases<いいよ, 来いよ>;

// Type checking examples
const correctPhrase1: いいよ = "いいよ"; // "It's good!"
const correctPhrase2: 来いよ = "来いよ"; // "Come on!"
const correctFullPhrase: いいよ来いよ = "いいよ、来いよ"; // "Good times, come on!"
// const incorrectPhrase: いいよ来いよ = "いいよ、くるよ"; // Error: verb form doesn't match
```

This way, we can use the type system to represent compound sentences composed of multiple independent short sentences.

## Conclusion and Future Prospects

This article explored Japanese grammatical structures through TypeScript's advanced type system. Starting with basic verb conjugations, we gradually built a type system capable of representing interrogative sentences, conditional sentences, and compound sentences.

Traditionally, writing code that uses complex type systems to annotate natural languages has been extremely costly and primarily used for showing off technical skills. However, the situation has fundamentally changed—as long as the type system is well-designed, LLMs can now accurately annotate TypeScript types for almost any natural language. This means that ordinary people, even without understanding TypeScript, can receive strongly-typed prompts from LLMs when learning languages. More interestingly, if you can write TypeScript, you can actually interpret the grammar of almost any natural language!

It's worth clarifying that this project's scope is strictly limited to one-way annotation of sentences through TypeScript's type system, without involving parsing arbitrary strings back into grammatical types—that's work better suited for LLMs. All example sentences in print language textbooks are weakly typed, and this method can provide strongly-typed guidance for learners.

This methodology of using TypeScript's type system to help learn languages can be extended to almost all natural languages. Based on this concept, I've established the [TypedGrammar](https://github.com/typedgrammar) organization on GitHub. The current Japanese type system code is already open-sourced at [github.com/typedgrammar/typed-japanese](https://github.com/typedgrammar/typed-japanese). Although it's currently just a technical prototype, developing it to the point where it has practical value for ordinary people's language learning is essentially patience-testing work, going from 1 to 100.

As we continue developing this synthesis of type systems and natural language, we're not just creating a programming curiosity—we're building a bridge between two worlds of expression that promises to make language learning more accessible, precise, and intellectually rewarding.
