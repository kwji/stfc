__Overview__

Damage in STFC can be broken down as follows:
1. `Damage Dealt` - Attacker's raw damage output 
2. `Damage Mitigated` - Portion of the attacker's `Damage Dealt` eliminated ("mitigated") by the defender's `Damage Mitigation`
3. `Damage Received` - The remaining un-mitigated portion of the attacker's `Damage Dealt`
    - `Shield Damage Received` - Portion of `Damage Received` applied to the defender's `Shield Health`.
    - `Hull Damage Received` - Portion of `Damaged Received` applied to the defender's `Hull Health`.

__Damage Dealt__

Damage Dealt is computed based on the attacker's weapons' `Base Damage` , the attacker's relevant `Damage Modifiers`, if the hit is a critical hit, and if the defender has an active _Hull Breach_
- `Damage Dealt` = `Base Damage` * `Damage Modifiers` * `Critical Multiplier` * `Hull Breach Multiplier` 
- `Base Damage` - Listed weapon damage with no officers assigned defined by the range `[Min Weapon Damage, Max Weapon Damage]`. Note that _some_ damage modifiers directly modify this `Base Damage` value (ex: _Damage Enhancer_/_Advanced Damage Enhancer_ territory services)
- `Damage Modifiers` - The sum of non-base-value damage bonuses and debuffs (ex: Building bonuses, Research bonuses, Officer attack bonus, Officer abilities, Below Decks abilities Ceritos Support, etc)
- `Critical Multiplier` - The sum of the ship's displayed _Critical Damage_ attack statistic and any relevant critical damage modifiers (ex: Officer abilities, non-global Research bonuses, Mantis Debuff, etc) if the weapon hit is a critical hit. Otherwise this is just `1` (that is `100%`)
- `Hull Breach Multiplier` - Final multiplier of `1.5` (that is `150%`) if the hit is a critical hit _and_ the defender has an active _Hull Breach_. Otherwise this is just `1` (that is `100%`)

__Damage Mitigated__

See the mitigation calculator to test things out: https://stfc-toolbox.now.sh/mitigation

The defender's `Damage Mitigated` is computed as a simple percentage of the attacker's `Damage Dealt` based on the defender's `Damage Mitigation`.
- `Damage Mitigated = Damage Dealt * Damage Mitigation`

The defender's `Damage Mitigation` value is the percentage of an attacker's `Damage Dealt` that is mitigated by the defender (ex: `50%` or `0.5`). 
- `Damage Mitigation` is a function of the defender's mitigation stats (`Shield Deflection`, `Armor`, `Evasion`) and the attacker's piercing stats (`Shield Piercing`, `Armor Piercing`, `Accuracy`) and is subject to diminishing returns, capping out at `~72%` or `0.72`.
- Each ship class has a primary mitigation stat that is weighted more heavily than the others when computing damage mitigation:
    - Explorers: `Shield Deflection`
    - Battleships: `Armor`
    - Interceptors: `Evasion`
- For example: All things being equal, a single point of additional `Shield Deflection` will result in a larger `Damage Mitigation` value for an explorer than for an interceptor or battleship. Correspondingly a single point of additional `Shield Piercing` will result in a greater decrease in `Damage Mitigation` for an explorer than for an interceptor or battleship.

__Damage Received__

`Damage Received` is what remains after subtracting `Damage Mitigated` from `Damage Dealt`
- `Damage Received` = `Damage Dealt` - `Damage Mitigated`

`Shield Damage Received` computed as a percentage of `Damage Received`. If this exceeds the defender's remaining `Shield Health` then the remaining amount is applied to the hull health instead.
- `Shield Damage Received` = `Damage Received` * `Shield Mitigation`
- `Shield Mitigation` - The percentage of `Damage Received` to subtract from the defender's `Shield Health`. `80%` by default but can be modified by officer abilities.

`Hull Damage Received` is what remains after subtracting `Shield Damage Received` from `Damage Received`. Once `Shield Health` has been depleted `Hull Damage Received` is equal to `Damage Received`
- `Hull Damage Received` = `Damage Received` - `Shield Damage Received`
- Once the defender's `Shield Health` is depleted, all `Damage Received` is applied to the defender's `Hull Health`.

Note: `Shield Mitigation` is a critical concept. Officers like `ST Una`, `Harrison`, `TOS Uhura`, and `Harry Mudd` function by altering the `Shield Mitigation` value from its default of `80%` in some way. 
- Rank 1 `Harrison` - Scales down the opponent's `Shield Mitigation` by `60%` for round 1 resulting in a `Shield Mitigation` value of `80% * 0.4 = 32%`. This means that `68%` of the opponent's `Damage Received` is applied their `Hull Health` directly instead of the standard `20%` for Round 1.
