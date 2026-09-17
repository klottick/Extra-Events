# Stellaris 4.5 compatibility

Target: Cygnus. Based on the preliminary release notes in developer diary 433:
https://steamstore-a.akamaihd.net/news/externalpost/steam_community_announcements/1844115010491664

## Implemented

- Use the vanilla `is_nomadic` trigger instead of interpreting a colony count as
  the empire's gameplay type.
- Require a planet carrier for natural-colony event eligibility.
- Restrict the Vortex and Mutiny entry events to conventional science ships:
  their fleet locks and ship-loss outcomes must not target Arkship colonies.
- Guard the delayed Vortex ship-loss and constructor-loss effects by ship class.
- Restrict the delayed destructive Mutiny outcome to science ships.
- Release surviving fleets at the Mutiny leader-death and rescue endings.
- Retain the merged Rock Pets planet-scope fixes and placeholder-origin removal.

No reward values, reward tiers, pulse weights, or event timing were adjusted.

## Verification status

The installed game used for inspecting vanilla scripting is **4.4.6**, not 4.5.
`is_nomadic`, `carrier_is_type = planet`, and the science-ship class check are
already used in that game's scripts. This is not a substitute for a 4.5 runtime
test. Keep the published descriptor at 4.4 until the checks below are complete.

The removed `pop_has_ethic` and `pop_group_has_ethic` triggers are not used by
this mod. Country-level ethics checks do not need conversion to pop-percentage
checks. The mod does not define AI economic plans or faction types requiring
the migrations described in the preliminary notes.

## Release checks on a fresh 4.5 game

4.5 changes the pop-group save format; do not use a 4.4 save as the test baseline.

- [ ] Compare final release notes with the preliminary notes.
- [ ] Inspect 4.5 `create_pop_group` documentation and vanilla examples. Verify
      the two explicit `ethos` blocks in `eev_popup.7` in
      `events/eev_country_events.txt`. If migration is required, affect only the
      newly created population, not an existing merged group.
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
- [ ] Check monthly-scaled resource rewards resolve their vanilla tier variables.
- [ ] Exercise pre-FTL observation chains with delayed callbacks and ownership
      changes; inspect error.log for missing targets and scope errors.
- [ ] Test with Nomads enabled and disabled, and test save/reload within 4.5.
- [ ] Update descriptor.mod and the local external launcher descriptor to
      `Extra Events 4.5 Continued` / `supported_version="v4.5.*"` after validation.

Use `-debug_mode` and `debugtooltip` for focused event tests. Search logs by
script filename as well as event namespace; this mod has namespaces other than
`eev_`. A long campaign is not required for these compatibility checks.
