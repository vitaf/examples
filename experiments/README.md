# MindCopy Educational Experiments

## Структура

```
experiments/
├── undergraduate/     # Бакалавриат — базовые концепции
├── graduate/          # Магистратура — системная нейронаука
├── doctoral/          # Аспирантура — продвинутые темы
└── README.md          # Этот файл
```

## Бакалавриат (4 эксперимента)

| # | Эксперимент | Концепция | Ключевой механизм |
|---|------------|----------|-------------------|
| 1 | action_potential | Потенциал действия | HH Na+/K+ каналы, порог, рефрактерность |
| 2 | passive_cable | Пассивный кабель | λ и τ, затухание сигнала |
| 3 | synaptic_integration | Синаптическая интеграция | EPSP/IPSP, временная и пространственная суммация |
| 4 | lateral_inhibition | Латеральное торможение | GABA-A, контрастное усиление |

## Магистратура (4 эксперимента)

| # | Эксперимент | Концепция | Ключевой механизм |
|---|------------|----------|-------------------|
| 5 | cpg_rhythm | Центральный генератор ритма | Полуцентровый осциллятор, SK/HCN, пост-ингибиторный ребаунд |
| 6 | working_memory | Рабочая память | Аттракторная динамика, NMDA, рекуррентное возбуждение |
| 7 | receptive_field | Рецептивное поле | Center-surround, горизонтальные клетки |
| 8 | stdp_learning | STDP и обучение | CaMKII/Calcineurin, AMPAR trafficking |

## Аспирантура (4 эксперимента)

| # | Эксперимент | Концепция | Ключевой механизм |
|---|------------|----------|-------------------|
| 9 | epileptiform | Эпилептиформная активность | E/I дисбаланс, PDS, KCC2, Kv7 |
| 10 | pharmacology | Фармакология каналов | Drug screening, dose-response, use-dependence |
| 11 | homeostatic_plasticity | Гомеостатическая пластичность | Synaptic scaling, TNFα, Arc, mTORC1 |
| 12 | bifurcations | Бифуркации и хаос | Hopf, SNIC, period-doubling, attractors |

## Сети для экспериментов

Все .bexp используют относительный путь `../test_single_neuron.bion`.
Полноценные сети для graduate/doctoral экспериментов описаны в `_bion_note`
каждого .bexp файла — они требуют ручной генерации через Build Panel
или `TadpoleDevelop`.

## Как использовать

### В Studio
1. Файл → Загрузить BION → `examples/test_single_neuron.bion` (или сгенерировать свою сеть)
2. Эксперимент → Загрузить → выбрать .bexp файл → ▶ Воспроизвести.

### Пакетно
```bash
biocore-daemon --batch --load-bexp <experiment>.bexp --ticks 10000 --bexp-output result.bexp
```

### Распределённо
```bash
curl -X POST http://orchestrator:8090/api/v1/task/create_experiment \
  -d '{"bexp_url":"<path>", "name":"<name>", "duration_ticks":10000}'
```

## Формат .bexp
Каждый эксперимент — файл в формате BION Experiment (.bexp):
- JSON-заголовок: описание, пробы, стимулы, детекция
- Бинарные кадры: запись V_m во время симуляции

## Биологические гарантии
- Все ионные каналы — полная кинетика Ходжкина-Хаксли/GHK
- Синапсы — реалистичная кинетика выброса/связывания/клиренса
- Пластичность — CaMKII/Calcineurin сигнальные пути
- Без суррогатов — все вычисления через полный биологический тик (core_run_tick_impl)
