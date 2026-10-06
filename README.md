# AI Agents for Science

## О проекте

**AI Agents for Science** — исследовательский проект по созданию полностью автоматизированной системы научных исследований, способной проходить полный цикл:

```text
idea generation
      ↓
code editing
      ↓
experiment execution
      ↓
result analysis
      ↓
paper writing
      ↓
automated review
      ↓
iterative refinement
```

Цель проекта — реализовать **end-to-end scientific discovery system**, которая с помощью foundation models умеет генерировать и развивать исследовательские идеи, писать и изменять код, запускать эксперименты, анализировать результаты, готовить научные тексты и итеративно улучшать их на основе автоматического ревью.

Отдельный фокус проекта — **robust MLE core**, безопасное выполнение кода, воспроизводимость экспериментов и тестирование системы на небольших локальных LLM.

---

## Моя зона ответственности

**Оркестратор системы.**

Основная задача — связать специализированные компоненты в единый управляемый research workflow и обеспечить корректное прохождение между этапами исследования.

Предполагаемые задачи оркестратора:

- управление последовательностью и состоянием research workflow;
- маршрутизация между специализированными агентами;
- запуск и контроль experiment/code execution;
- передача результатов между этапами;
- retries, timeouts и обработка ошибок;
- хранение промежуточных артефактов и результатов экспериментов;
- поддержка итеративного цикла `idea → experiment → review → refinement`;
- tracing и observability;
- интеграция с локальными и внешними LLM;
- обеспечение воспроизводимости запусков;
- взаимодействие с sandboxed execution environment.

Точный набор обязанностей и интерфейсов будет уточняться по мере согласования архитектуры внутри команды.

---

## Архитектура проекта

Предварительно система рассматривается как набор специализированных агентов:

| Компонент | Назначение |
|---|---|
| **Generator** | Генерация новых исследовательских идей или развитие существующих |
| **Engineer** | Написание и изменение исследовательского кода |
| **Experimentator** | Запуск вычислительных экспериментов |
| **Author** | Анализ результатов и подготовка manuscript |
| **Automated Reviewer** | Оценка drafts и формирование feedback |
| **Orchestrator** | Координация всех компонентов и управление research lifecycle |

```text
                        ┌──────────────────┐
                        │    Generator     │
                        │ ideas/hypotheses │
                        └────────┬─────────┘
                                 │
                                 ▼
                        ┌──────────────────┐
                        │   Orchestrator   │
                        │ state / routing  │
                        │ execution policy │
                        └───────┬──────────┘
                                │
                ┌───────────────┼───────────────┐
                ▼               ▼               ▼
        ┌──────────────┐ ┌──────────────┐ ┌──────────────┐
        │   Engineer   │ │Experimentator│ │   Reviewer   │
        │ code editing │ │ experiments  │ │  evaluation  │
        └──────┬───────┘ └──────┬───────┘ └──────┬───────┘
               │                │                │
               └────────────────┼────────────────┘
                                ▼
                        ┌──────────────────┐
                        │      Author      │
                        │ analysis / paper │
                        └────────┬─────────┘
                                 │
                                 └──────► iterative loop
```

---

## Основные задачи проекта

Согласно исходной постановке проекта:

1. Реализовать **LLM-driven pipeline** для:
   - idea generation;
   - code editing;
   - experiment execution;
   - scientific writing.

2. Улучшить **automated reviewer**:
   - добавить Vision-Language Models;
   - анализировать figures;
   - учитывать layout и визуальное качество материалов.

3. Провести benchmark системы в разных научных областях:
   - diffusion modelling;
   - language modelling;
   - другие AI-задачи;
   - потенциально chemistry / biology и другие домены.

4. Обеспечить **ethics & safety**:
   - прозрачность действий системы;
   - sandboxed execution;
   - контроль выполнения кода;
   - соблюдение ограничений исследовательской среды.

5. Анализировать качество результатов:
   - peer-review scores;
   - качество отдельных стадий pipeline;
   - ошибки и failure modes;
   - влияние итеративного feedback loop.

---

## Литературная база

На первом этапе проекта изучаются современные подходы к autonomous research agents, harness engineering и evaluation AI-систем.

Основные идеи, которые могут быть полезны при проектировании оркестратора:

- **Harness Engineering** — качество системы зависит не только от модели, но и от tools, context, workflow и execution policy;
- **Long-Horizon Research** — для длительных исследований требуется сохраняемое и воспроизводимое состояние;
- **Chain-of-Evidence** — scientific claims должны быть связаны с реальным кодом, экспериментами, логами и литературой;
- **Robustness Evaluation** — правильный результат не всегда означает устойчивое reasoning;
- **Predictive Evaluation** — полезно оценивать вероятность успеха конкретной конфигурации `model + harness` ещё до дорогого запуска.

Эти работы используются как **теоретическая база для проектирования**, а не заменяют исходную постановку AI Agents for Science.

---

## План работы на год

### Checkpoint 1 — Постановка задачи и анализ существующих решений

- изучить назначенные статьи и существующие autonomous research systems;
- зафиксировать архитектуру текущего проекта;
- определить роли агентов и границы ответственности оркестратора;
- описать agent/tool interfaces;
- определить формат состояния research workflow;
- определить требования к experiment execution и observability;
- подготовить план реализации MVP.

**Результат:** архитектурная схема, описание interfaces и план реализации.

### Checkpoint 2 — MVP оркестратора

- реализовать базовый orchestration loop;
- реализовать вызов и координацию агентов;
- добавить structured input/output между компонентами;
- реализовать управление состоянием workflow;
- добавить retries, timeouts и error handling;
- реализовать базовый experiment execution;
- добавить unit/integration tests.

**Результат:** минимальный end-to-end pipeline, проходящий несколько стадий research workflow.

### Checkpoint 3 — Интеграция research pipeline

- интегрировать реальные Generator / Engineer / Experimentator / Reviewer / Author компоненты;
- реализовать итеративный цикл улучшения;
- добавить persistent artifacts;
- обеспечить воспроизводимость экспериментов;
- интегрировать sandboxed code execution;
- добавить tracing и observability;
- протестировать работу с локальными LLM.

**Результат:** работающий прототип автономного scientific discovery pipeline.

### Checkpoint 4 — Evaluation и надёжность

- определить benchmark и evaluation protocol;
- исследовать failure modes;
- измерить стабильность многошаговых research trajectories;
- оценить влияние отдельных компонентов;
- провести ablation experiments;
- оценить latency / compute / token cost;
- проверить устойчивость recovery/retry механизмов.

**Результат:** экспериментальная оценка оркестратора и анализ ограничений системы.

### Final Checkpoint — Итоговая система

- стабилизировать end-to-end pipeline;
- провести финальные эксперименты;
- сравнить варианты orchestration;
- подготовить воспроизводимое demo;
- оформить документацию и результаты;
- оценить возможность использования результатов в научной публикации.

**Результат:** end-to-end AI Agents for Science prototype с воспроизводимой orchestration и evaluation.

---

## Команда

### Руководители проекта

- **Ilya Makarov** — Team Lead 
- **Aleksei Stepin** — Researcher 
- **Mikhail Mozikov** — Researcher

| Участник | Зона ответственности |
|---|---|
| **Никита Бурлака** | Orchestrator / AI Engineering |

---

## Предполагаемый стек

Финальный стек будет определён после согласования архитектуры.

Предварительно:

- Python
- LLM / VLM
- agent orchestration
- structured schemas
- Docker / sandboxed execution
- local LLM inference
- experiment tracking
- tracing / observability
- automated testing

---

## Структура репозитория

```text
ai-agents-for-science/
├── README.md
├── docs/
│   ├── architecture/
│   └── literature/
├── src/
│   ├── orchestrator/
│   ├── agents/
│   ├── tools/
│   ├── execution/
│   └── evaluation/
├── experiments/
├── configs/
├── tests/
├── scripts/
└── pyproject.toml
```

---


## Related Work

- [The AI Scientist: Towards Fully Automated Open-Ended Scientific Discovery](https://arxiv.org/abs/2408.06292)
- [The AI Scientist-v2](https://arxiv.org/abs/2504.08066)
- [AIDE: AI-Driven Exploration in the Space of Code](https://arxiv.org/abs/2502.13138)
- *Autonomous chemical research with large language models*, Nature, 2023
- *Mathematical discoveries from program search with large language models*, DeepMind, 2023

---
