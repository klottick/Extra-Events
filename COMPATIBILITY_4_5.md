# Stellaris 4.5 compatibility

Target: Cygnus. Release review against the installed **4.5.1 (358e)** scripts.

- 4.5 release: https://steamstore-a.akamaihd.net/news/externalpost/steam_community_announcements/1844115010503624
- 4.5.1 hotfix: https://steamstore-a.akamaihd.net/news/externalpost/steam_community_announcements/1844751498221872

## Implemented

- Use the vanilla `is_nomadic` trigger instead of interpreting a colony count as
  the empire's gameplay type.
- Require a planet carrier for natural-colony event eligibility.
- Restrict the Vortex and Mutiny entry events to conventional science ships:
  their fleet locks and ship-loss outcomes must not target Arkship colonies.
- Guard the delayed Vortex ship-loss and constructor-loss effects by ship class.
- Dispatch Vortex project success events in the completing ship's scope, so
  constructor/military event targets do not incorrectly refer to the science ship.
- Restrict the delayed destructive Mutiny outcome to science ships.
- Release surviving fleets at the Mutiny leader-death and rescue endings.
- Retain the merged Rock Pets planet-scope fixes and placeholder-origin removal.

No reward values, reward tiers, pulse weights, or event timing were adjusted.

## Verification status

The installed game is **4.5.1 (358e)**, confirmed in launcher-settings.json.
Static source verification is complete for the points below; in-game event tests
have not been performed. Keep the published descriptor at 4.4 until the runtime
checks below are complete.

Evidence in the released game files (paths relative to the installation):

- `common/scripted_triggers/09_scripted_triggers_nomads.txt` still uses
  `is_nomadic = no`; `events/game_start.txt` uses `carrier_is_type = planet`.
- `common/ship_sizes/29_nomads_dlc_ships.txt` assigns all Arkship tiers to
  `shipclass_starbase`, including scientific Arkships. The science-ship guards
  therefore exclude vanilla Arkships. Nomad logistics ships are constructors.
- `events/colony_events_1.txt:1500` still creates pop groups using an `ethos`
  block. No evidence requires replacing this syntax with post-creation ethics
  changes. The mod's exact fanatic-Spiritualist outcome still needs a runtime check.
- `common/scripted_effects/00_scripted_effects.txt:8826` defines `kill_single_pop`
  as `kill_pop_group` with `amount = 100`, not deletion of an entire group.
- All 30 distinct tier reward/min/max variables referenced by this mod exist in
  `common/scripted_variables/00_scripted_variables.txt`.
- `common/global_ship_designs/event_ship_designs_general.txt:132` still defines
  `NAME_Dagger` as an equipped event corvette.
- `common/special_projects/documentation.txt` specifies ship scope for successful
  ship projects, but country scope for failure/cancellation. The Vortex success
  callbacks now retain ship scope; failure/cancellation still use saved targets.

4.5 extends planet flags to colony/carrier/ship scopes; explicit planet wrappers
remain appropriate for physically planet-bound events. This does not establish
that `planet_event` is valid in colony scope.

4.5.1 additionally reports duplicate database keys as errors. The hotfix's other
listed changes do not require changes to this mod's script APIs.

The removed `pop_has_ethic` and `pop_group_has_ethic` triggers are not used by
this mod. Country-level ethics checks do not need conversion to pop-percentage
checks. The mod does not define AI economic plans or faction types requiring
the migrations described in the preliminary notes.

## Release checks on a fresh 4.5 game

4.5 changes the pop-group save format; do not use a 4.4 save as the test baseline.

- [x] Review the release announcement, 4.5.1 hotfix notes, and shipped scripts
      against the preliminary compatibility assumptions.
- [x] Inspect released vanilla `create_pop_group` examples: `ethos` is still used.
- [ ] Verify the exact two `ethos` outcomes in `eev_popup.7` in-game.
- [ ] Exercise pop creation in Country Events, Illegal Aliens, and Space Nomads;
      check population amounts, species, and ethics after a monthly tick.
- [ ] Exercise pop loss in Murder, Misplaced, and Faint Asteroid; confirm the
      intended loss rather than loss of an entire mixed-ethics group.
- [ ] Run Rock Pets on an eligible settled colony; confirm deposit, reward,
      flag, and no invalid-scope messages. Confirm Arkships are ineligible.
- [ ] Verify Vortex and Mutiny cannot start on an Arkship, including through
      anomaly research/Deep Scan. Exercise ordinary science-ship success,
      failure, cancellation, and delayed outcomes; check surviving fleet locks.
- [ ] Verify rescue callbacks identify the participating ships correctly and
      do not strand fleets when a saved target disappears.
- [ ] Check the three NAME_Dagger ship rewards for valid designs and ownership.
- [ ] Check energy/mineral grants and affordability checks against Nomadic
      Operational Reserves where those events are accessible.
- [x] Check that all referenced monthly reward tier variables exist in 4.5.1.
- [ ] Check actual monthly-scaled payouts in-game.
- [ ] Exercise pre-FTL observation chains with delayed callbacks and ownership
      changes; inspect error.log for missing targets and scope errors.
- [ ] Test with Nomads enabled and disabled, and test save/reload within 4.5.
- [ ] Update descriptor.mod and the local external launcher descriptor to
      `Extra Events 4.5 Continued` / `supported_version="v4.5.*"` after validation.

Use `-debug_mode` and `debugtooltip` for focused event tests. Search logs by
script filename as well as event namespace; this mod has namespaces other than
`eev_`. A long campaign is not required for these compatibility checks.
