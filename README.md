# OCUDU ↔ OAI UE ↔ AODT Bridge

A Python/ZMQ bridge that applies AODT channel impulse responses (CIRs) and optional noise to the downlink and uplink IQ streams between an OCUDU gNB and an OAI UE.

```text
OCUDU gNB ↔ [AODT CIR + FIR + AWGN] ↔ OAI UE
```

## Requirements

- Python 3 with `numpy`, `pyzmq`, `scipy`, and `PyYAML`
- AODT `dt_client` bindings and a running AODT server
- OCUDU gNB and OAI `nr-uesoftmodem` with ZMQ radio support

`bridge_sync` expects the AODT source tree at `aodt_source/`, including its virtual environment, client build, bridge scripts, and scenario YAML.

## Run

1. Start the AODT server.
2. Start the bridge:

   ```bash
   ./bridge_sync
   ```

3. Start the gNB and UE.
4. After the UE attaches, start channel variation:

   ```bash
   touch /tmp/phyan100_start_channel_variation
   ```

To trigger automatically, use `BRIDGE_INITIAL_HOLD_SECONDS=<seconds>`. Extra command-line arguments are passed to the bridge, for example:

```bash
./bridge_sync --ue-delay-us 1.0
```

## Common configuration

| Variable | Default | Purpose |
|---|---:|---|
| `BRIDGE_SERVER_ADDRESS` | `localhost:50051` | AODT server |
| `BRIDGE_YAML_FILE` | `aodt_source/client/examples/msu_1ru_1ue.yaml` | Scenario file |
| `BRIDGE_SAMPLE_RATE_HZ` | `122880000` | IQ sample rate |
| `BRIDGE_NOISE_POWER_DBFS` | `-65` | Noise floor; empty uses `BRIDGE_SNR_DB` |
| `BRIDGE_SNR_DB` | `30` | Relative SNR; `off` disables it |
| `BRIDGE_PATH_LOSS_DB` | `0.0` | Additional path loss |
| `BRIDGE_LOOP_SCENARIO` | `off` | Repeat the AODT scenario |

The sample-synchronous bridge changes CIRs by cumulative IQ sample count and keeps DL/UL on a common time origin. Restart the gNB and UE whenever the bridge restarts, or the sample time base may be misaligned.
