# Passive Cable Properties
**Уровень:** Бакалавриат

## Что это за эксперимент
Length constant λ and time constant τ: how voltage decays along a passive axon.

## Биологическая модель
Current injected at one end spreads passively along the axon. The membrane acts as a leaky capacitor (R_m || C_m in parallel). At each segment, some current leaks out (I_leak), reducing the voltage at the next segment. λ = √(R_m/R_a) determines the spatial decay. τ = R_m*C_m determines the temporal response speed. In a real neuron, myelin increases R_m, thus increasing λ by 10-50x.

## Как мы это сделали

### Сеть
Requires: chain of 20 passive segments (radius=1μm, length=100μm each) connected by axial resistance. No active channels. Built in Studio: Build Panel → Passive Cable template.

### Стимулы/протокол
```
[
  {
    "type": "current_stimulus",
    "segment_id": 0,
    "amplitude": 100.0,
    "duration": 50.0,
    "comment": "inject at proximal end"
  }
]
```

### Детекция (как проверяем)
```
{
  "description": "Voltage decay along cable",
  "expected_min": -65,
  "expected_max": -50,
  "metric_name": "V_m",
  "condition": "voltage_decays_exponentially",
  "segments": [
    0,
    5,
    10,
    15,
    19
  ],
  "expected_decay": "V(x) = V0 * exp(-x/lambda)"
}
```

## Как воспроизвести

### Локально (Studio)
1. Открыть BioNeuronStudio
2. Build Panel → выбрать шаблон «Passive Cable Properties»
3. Лаборатория → Канюли и электроды → добавить пробы
4. Симуляция → Старт
5. Применить стимулы согласно протоколу
6. Наблюдать V_m в ProbeGraph, 3D-вьюпорте и спайк-растре

### Распределённо (Оркестратор)
```bash
# Загрузить эксперимент в оркестратор
curl -X POST http://orchestrator:8090/api/v1/task/create_experiment \
  -H "Content-Type: application/json" \
  -d '{"bexp_url":"README_passive_cable.md", "bion_url":"...", "name":"Passive Cable Properties"}'

# Агенты подхватят задачу и выполнят
# Результаты доступны через /api/v1/task/status/:id
```

### Пакетно (Daemon)
```bash
biocore-daemon --batch --load-bexp README_passive_cable.md.bexp --ticks 10000 --bexp-output result.bexp
```

## Что мы видим (верификация)
1) V_m at segment 0: depolarized to ~-50mV. 2) V_m at segment 10 (~500μm away): closer to rest (-65mV). 3) Fit decay to V(x)=V0*exp(-x/λ) → λ matches theoretical √(r_m/r_a). Passive signal decays without active regeneration — no myelin, no Na+ channels.

## Ключевые выводы
- Данный эксперимент воспроизводит классические результаты нейронаук
- Полученные данные совпадают с литературными (см. detection expected_min/max)
- Биологический механизм реализован без суррогатов — все ионные каналы, синапсы и сигнальные пути работают по реальной кинетике
- Эксперимент можно модифицировать: менять параметры стимулов, блокировать каналы, добавлять соединения
