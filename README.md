# Appium + Android Studio - POC (TODO)

**Autor:** [Caio Cavalcanti](https://github.com/CesarImperas) - PDI @ VIRTUS UFCG  

**Tópicos pendentes:**
- O próximo marco desta etapa é criar uma sessão no **Appium Inspector**, inspecionar a UI do APK e registrar os locators encontrados antes da implementação do primeiro teste em Java.

> Boa parte do material desenvolvido como estudo da ferramenta e outros assuntos relacinados, estão inteiros no *Obsidian* e podem ser consultados se solicitados pela autoria.

## Objetivo da POC

Avaliar o uso do **Appium para automação funcional/UI E2E de aplicações Android nativas**, com foco nos seguintes aspectos:

- Identificação e interação com elementos da interface;
- Estabilidade dos testes e ocorrência de flakiness;
- Sincronização e estratégias de espera;
- Organização e manutenibilidade da automação;
- Limitações encontradas durante a implementação;
- Viabilidade de integração futura com CI/CD.

> **Escopo:** esta POC utiliza **Appium** como ferramenta de automação E2E/UI. Testes instrumentados nativos, como Espresso, não fazem parte desta avaliação.

## Estrutura inicial do repositório

```text
appium-poc/
│
├── app/
│   └── appForTests.apk
│
├── src/
│   └── test/
│       └── java/
│           ├── pages/
│           ├── tests/
│           └── utils/
│
├── pom.xml
├── README.md
└── .gitignore
```

### Por que não criar `config/`, `drivers/`, `factories/`, `hooks/` etc. agora?

Porque ainda não sabemos se serão necessários.

Depois do primeiro teste, provavelmente teremos algo como:

```text
tests
  ↓
pages
  ↓
driver
  ↓
Appium Server
  ↓
UiAutomator2
  ↓
Android Emulator / Device
  ↓
Aplicação
```

> A estrutura será evoluída conforme as necessidades observadas nos testes. A intenção da POC é avaliar o Appium e sua manutenibilidade, não construir um framework abstrato por antecipação.

## Stack tecnológica da POC

| Componente           | Escolha                        |
| -------------------- | ------------------------------ |
| Linguagem            | **Java**                       |
| Build / dependências | **Maven**                      |
| Test runner          | **JUnit 5**                    |
| Automação            | **Appium**                     |
| Driver Android       | **UiAutomator2**               |
| Protocolo            | **WebDriver**                  |
| Android              | Android Studio + SDK           |
| Dispositivo          | Android Emulator / AVD         |
| APK                  | APK disponibilizado pelo curso |
| Inspector            | Appium Inspector               |
| IDE                  | VS Code / IntelliJ IDEA        |
| Versionamento        | Git                            |

## Pré-requisitos

Antes da execução dos testes, o ambiente deve disponibilizar:

- JDK;
- Node.js e npm;
- Android Studio e Android SDK;
- `adb`;
- Android Emulator / AVD;
- Appium Server;
- driver UiAutomator2;
- Appium Inspector;
- Maven;
- Git.

### Validação rápida do ambiente

```bash
java -version
node -v
npm -v

adb devices

appium --version
appium driver list --installed
```

O objetivo dessas verificações é confirmar o ambiente antes de investigar problemas relacionados ao próprio teste.

## Appium Inspector

O **Appium Inspector** é utilizado nesta POC para visualizar e explorar a interface da aplicação durante uma sessão Appium. Ele apresenta a captura da tela, a árvore da UI e os atributos dos elementos, permitindo investigar locators e interações antes de codificar os testes.

O Inspector é disponibilizado oficialmente como aplicação desktop para macOS, Windows e Linux. O download deve ser feito pela página oficial de releases do projeto.

### Fluxo de uso na POC

```text
Android Emulator / Device
          ↑
         ADB
          ↑
     UiAutomator2
          ↑
    Appium Server
          ↑
  Appium Inspector
```

A sequência prática é:

1. iniciar o Android Emulator / AVD;
2. confirmar o dispositivo com `adb devices`;
3. iniciar o Appium Server;
4. abrir o Appium Inspector;
5. criar uma sessão apontando para o servidor Appium;
6. informar as capabilities necessárias para o APK;
7. explorar a árvore da UI e os atributos dos elementos;
8. registrar os locators candidatos para o primeiro teste.

### Sessão inicial

Parâmetros esperados para o servidor local:

```text
Host: 127.0.0.1
Port: 4723
Path: /
```

Exemplo de capabilities para a aplicação da POC:

```json
{
  "platformName": "Android",
  "appium:automationName": "UiAutomator2",
  "appium:deviceName": "emulator-5554",
  "appium:app": "/caminho/absoluto/appium-poc/app/appForTests.apk"
}
```

> O `deviceName` deve ser ajustado para o identificador exibido pelo `adb devices`, e o caminho do APK deve ser absoluto na configuração da sessão do Inspector.

## Elementos e Locators

A investigação dos elementos deve priorizar atributos que representem o elemento de forma estável e significativa para o teste.

A POC utilizará inicialmente a seguinte **ordem de avaliação**, que será validada na prática e não tratada como regra universal:

1. **Accessibility ID**;
2. **Resource ID**;
3. **Locators específicos do Android**;
4. **XPath**.

O principal critério é a **manutenibilidade e estabilidade do locator**, e não apenas a facilidade de encontrá-lo no Inspector.

### Registro dos locators

Durante a inspeção, registrar os candidatos relevantes para cada elemento:

| Elemento | Accessibility ID | Resource ID | XPath | Observação |
| -------- | ---------------- | ----------- | ----- | ---------- |
| —        | —                | —           | —     | —          |

Esse registro serve como evidência da POC e ajuda a comparar as estratégias durante a implementação dos primeiros fluxos.

## Execução esperada da automação

```text
JUnit 5
   ↓
Appium Java Client
   ↓
Appium Server
   ↓
UiAutomator2 Driver
   ↓
ADB
   ↓
Android Emulator / Device
   ↓
Appium App
```

O **Appium Client** será a biblioteca utilizada pelo código Java para estabelecer a sessão e enviar os comandos de automação ao Appium Server.

## Critérios de avaliação

Os resultados da POC devem registrar evidências sobre:

| Critério | O que observar |
| -------- | -------------- |
| Elementos | facilidade de inspeção e disponibilidade de atributos estáveis |
| Locators | clareza, estabilidade e acoplamento à implementação da UI |
| Sincronização | necessidade de waits e comportamento em diferentes tempos de resposta |
| Estabilidade | falhas intermitentes e necessidade de retries |
| Manutenibilidade | facilidade para alterar fluxos e elementos |
| Execução | tempo e comportamento durante os testes |
| Limitações | problemas encontrados no Appium, driver ou ambiente |
| CI/CD | requisitos e dificuldades para uma execução futura em pipeline |

## Roadmap da POC

| Etapa | Foco | Status |
| ----- | ---- | ------ |
| Day 1 | Ambiente Android Studio / SDK / Emulator | ✅ Concluído |
| Day 2 | Appium Server + UiAutomator2 + validação do ambiente | ✅ Concluído |
| Day 3 | Primeiro contato com Appium Inspector e primeiro teste | 🔄 Em andamento |
| Day 4 | Elementos e Locators | 🔄 Em andamento |
| Day 5 | Fluxos reais | ⏳ Próximo |
| Day 6 | Estrutura do framework | ⏳ Próximo |
| Day 7 | Consolidação, limitações e resultados | ⏳ Próximo |

## Referências

- [Appium Documentation](https://appium.io/docs/)
- [Appium Inspector](https://github.com/appium/appium-inspector)
- [Appium Inspector Releases](https://github.com/appium/appium-inspector/releases)
- [Appium Java Client](https://github.com/appium/java-client)
- [UiAutomator2 Driver](https://appium.io/docs/en/latest/quickstart/uiautomator2-driver/)
