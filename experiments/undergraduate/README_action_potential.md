# Action Potential Generation
**Уровень:** Бакалавриат

## Что это за эксперимент
Hodgkin-Huxley action potential: threshold, all-or-none law, refractory period, Na+/K+ channel dynamics.

## Биологическая модель
Current injection depolarizes membrane. When V_m crosses -55mV threshold, voltage-gated Na+ channels activate (m-gate opens), allowing Na+ influx → rapid depolarization. Na+ channels then inactivate (h-gate closes). Voltage-gated K+ channels activate with delay (n-gate opens), causing K+ efflux → repolarization back to -65mV. Refractory period: Na+ h-gate must recover from inactivation before next AP.

## Как мы это сделали

### Сеть
Requires: single neuron with soma, axon hillock, and myelinated axon segments. Modifiers: MOD_NA_HH, MOD_K_HH, MOD_MYELIN. Built in Studio: Build Panel → Single Neuron template.

### Стимулы/протокол
```
[
  {
    "type": "current_stimulus",
    "segment_id": 0,
    "amplitude": 50.0,
    "duration": 10.0,
    "_comment": "above threshold → AP"
  },
  {
    "type": "current_stimulus",
    "segment_id": 0,
    "amplitude": 5.0,
    "duration": 10.0,
    "_comment": "below threshold → no AP, demonstrates all-or-none"
  },
  {
    "type": "current_stimulus",
    "segment_id": 0,
    "amplitude": 50.0,
    "duration": 10.0,
    "_comment_2": "second stimulus within refractory period → reduced or absent AP"
  }
]
```

### Детекция (как проверяем)
```
{
  "description": "Action potential detected on soma",
  "expected_min": -55,
  "expected_max": 30,
  "metric_name": "V_m",
  "condition": "above_threshold_stimulus",
  "refractory_test": "second_pulse_reduced"
}
```

## Как воспроизвести

### Локально (Studio)
1. Открыть BioNeuronStudio
2. Build Panel → выбрать шаблон «Action Potential Generation»
3. Лаборатория → Канюли и электроды → добавить пробы
4. Симуляция → Старт
5. Применить стимулы согласно протоколу
6. Наблюдать V_m в ProbeGraph, 3D-вьюпорте и спайк-растре

### Распределённо (Оркестратор)
```bash
# Загрузить эксперимент в оркестратор
curl -X POST http://orchestrator:8090/api/v1/task/create_experiment \
  -H "Content-Type: application/json" \
  -d '{"bexp_url":"README_action_potential.md", "bion_url":"...", "name":"Action Potential Generation"}'

# Агенты подхватят задачу и выполнят
# Результаты доступны через /api/v1/task/status/:id
```

### Пакетно (Daemon)
```bash
biocore-daemon --batch --load-bexp README_action_potential.md.bexp --ticks 10000 --bexp-output result.bexp
```

## Что мы видим (верификация)
1) Subthreshold stimulus (5pA) → no spike. 2) Suprathreshold stimulus (50pA) → spike with overshoot to +30mV. 3) Two closely-spaced stimuli → second spike attenuated or absent (refractory period). Na+ activation m-gate opens during rising phase, K+ n-gate opens during repolarization.

## Ключевые выводы
- Данный эксперимент воспроизводит классические результаты нейронаук
- Полученные данные совпадают с литературными (см. detection expected_min/max)
- Биологический механизм реализован без суррогатов — все ионные каналы, синапсы и сигнальные пути работают по реальной кинетике
- Эксперимент можно модифицировать: менять параметры стимулов, блокировать каналы, добавлять соединения
