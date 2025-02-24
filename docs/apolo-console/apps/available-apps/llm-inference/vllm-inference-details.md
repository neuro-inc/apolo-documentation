# vLLM Inference details

### Under the Hood

When you specify a multi-GPU preset (like a preset with multiple Nvidia and AMD GPUs), **LLM Inference**:

1. **Determines GPU Provider & Count**
   * AMD MI210 → `gpuProvider=amd`, 2 GPUs.
   * NVIDIA → `gpuProvider=nvidia`, 2 GPUs.
2. **Sets GPU Visibility**
   * On AMD: `HIP_VISIBLE_DEVICES=0,1`, `ROCR_VISIBLE_DEVICES=0,1` (if 2 GPUs).
   * On NVIDIA: `CUDA_VISIBLE_DEVICES=0,1`.
3. **Applies a Default Parallel** arguments (e.g. `--tensor-parallel-size=2`) if the user hasn’t already done so.

#### Environment Variables for AMD

By default, if you select a preset with AMD GPU cards, the chart’s logic sets:

* **`HIP_VISIBLE_DEVICES=0,1...`** and **`ROCR_VISIBLE_DEVICES=0,1...`**` ``depending on number of available GPUs:` Tells ROCm which GPUs are accessible.
* **`TORCH_USE_HIP_DSA=1`**: Enables direct storage access for HIP.
* **`HSA_FORCE_FINE_GRAIN_PCIE=1`** & **`HSA_ENABLE_SDMA=1`**: Improves GPU ↔ Host & GPU ↔ GPU memory transfers.
* **`ROCM_DISABLE_CU_MASK=0`**: All compute units remain active.
* **`VLLM_WORKER_MULTIPROC_METHOD=spawn`**: Avoids “fork” issues on AMD.
* **`NCCL_P2P_DISABLE=0`**: By default, we assume your cluster has correct kernel parameters for GPU–GPU direct memory access. If not, you can pass `--set envAmd.NCCL_P2P_DISABLE=1` to forcibly disable P2P.

### Final Notes

* **NCCL / RCCL** logs will appear in the vLLM container logs. Look for lines referencing peer-to-peer if you see a hang.
* On AMD, if you do have persistent hangs, append `--set "envAmd.NCCL_P2P_DISABLE=1"` to your Apolo command to force fallback GPU communication.&#x20;
* For the best performance, we keep ROCm version 6.2+ or 6.3+ in sync with your Docker image (`rocm/vllm-ci`)&#x20;

By combining the right Apolo preset, environment variables, you can reliably run **vLLM** on multiple GPUs—be they AMD or NVIDIA —and get high token throughput for large LLMs.
