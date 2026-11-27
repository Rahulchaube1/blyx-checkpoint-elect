# Checkpoint Elect

Keep the best model checkpoint under integer loss minimization and restore it on a stop request.

Machine learning and evaluation · framework · 0.1.1

## Drive the workflow

Start with `checkpoint_elect_start()`. For each event call `checkpoint_elect_step(snapshot, event, value)`, persist the returned snapshot, and dispatch `checkpoint_elect_command(snapshot)` to your application’s adapter. The host owns I/O, event ordering, duplicate detection and durable command delivery. A matching event produces one command. Rejected events retain the phase and saved quantity. An invalid snapshot yields an empty vector.

The snapshot contains phase ID, saved quantity and last command ID. Event values are whole numbers from zero through one billion. In the table, **x** is the event value and **r** is the saved quantity. Rules match in listed order. To validate a whole history without running effects use `checkpoint_elect_replay(events, values)`. It returns the final snapshot or an empty vector on rejection.

| Current phase | Event | Guard | Next phase | Command | New quantity |
|---|---|---|---|---|---|
| idle | candidate | always | tracking | save_best | x |
| tracking | candidate | x<r | tracking | replace_best | x |
| tracking | candidate | x>=r | tracking | retain_best | r |
| tracking | stop | always | restoring | load_best | r |
| restoring | loaded | x=r | done | finalize_fit | r |

Phase IDs in order from zero: idle, tracking, restoring, done.

## Replay your own events

After installing the demo, run the following from its directory. Supply alternating event names and integer values. The command prints each requested action and exits with status 1 at the first rejected transition, or 2 for malformed arguments.

```sh
blyxc native-run main.blyx -- candidate 60 candidate 66 candidate 49 stop 0 loaded 49
```

## Try the published project

```sh
nest demo checkpoint_elect@0.1.1
blyxc native-run checkpoint_elect-demo/main.blyx
```

Install with `nest add checkpoint_elect@0.1.1` and import `vendor::checkpoint_elect::checkpoint_elect`.

Maintained by Rahul Chaube. [Blyx](https://www.blyx-lang.space) · [GitHub](https://github.com/Rahulchaube1) · [LinkedIn](https://www.linkedin.com/in/rahulchaube1) · [Sponsor](https://github.com/sponsors/Rahulchaube1)
