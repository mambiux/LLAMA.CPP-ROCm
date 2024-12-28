# LLAMA.CPP-ROCm

🚀 Llama on ROCm AMD Ryzen APU Guide 🧠⚡

    🔥 Unleashing : Experimental llama.cpp ROCm APU AI Inference Server 🔥

A step-by-step guide to setting up llama.cpp with ROCm on AMD APUs with awesome performance

Welcome to the ultimate guide to building your own AI AMD inference server! 
This repository is packed with everything you need to replicate my success of getting llama.cpp work well with ROCm on a Ryzen 7 5700U-powered system.

Whether you're a hobbyist, developer, or researcher, I hope this guide will help you harness the power of your AMD hardware to run AI models efficiently.

This build is a showcase of how to unlock the potential of an AMD Ryzen 7 5700U integrated GPU, delivering exceptional AI inference performance with minimal resources.

💻 About the Build

This server was created by repurposing a salvaged Lenovo V14 G2 Ryzen 7 5700U laptop with:

    24GB DDR4 3200MHz RAM (8GB soldered + 16GB stick).
    Running Ubuntu 24 for peak stability and compatibility.

The screen, keyboard, and mouse were removed, transforming the laptop into a dedicated AI server. The results? Llama.cpp running directly on the APU with all but one CPU core left idle—showing how AMD APUs can deliver both performance and efficiency for AI inference tasks.

This laptop-turned-server is a low-wattage, high-efficiency powerhouse, purpose-built for lightweight AI workloads. Let’s dive into the details!


1️⃣ Install Required Packages

First, install the necessary ROCm and development packages:

# Install ROCm runtime and development packages
    sudo apt-get install rocm-hip-runtime-dev rocm-hip-sdk clinfo radeontop
    sudo apt-get install rocm-dev rocm-libs rocminfo

# Install additional ROCm components
    sudo apt-get install rocm-hip-runtime-dev rocm-hip-sdk

2️⃣ Configure System Repositories
Enable required Ubuntu repositories and add ROCm sources:

# Add standard Ubuntu repositories
    sudo add-apt-repository main
    sudo add-apt-repository universe
    sudo add-apt-repository multiverse
    sudo apt update

# Add graphics drivers PPA
    sudo add-apt-repository ppa:oibaf/graphics-drivers
    sudo apt update
    sudo apt upgrade

3️⃣ Configure ROCm Repository
Edit the ROCm repository configuration:

# Edit ROCm repository file
    sudo nano /etc/apt/sources.list.d/rocm.list

# Add this line to the file:
    deb [arch=amd64] https://repo.radeon.com/rocm/apt/5.6 focal main

4️⃣ Configure OpenCL
Set up AMD's OpenCL implementation:

# Create/edit the AMD OpenCL ICD file
    sudo nano /etc/OpenCL/vendors/amdocl64.icd

# Add this line to the file:
    /opt/rocm/opencl/lib/libamdocl64.so

5️⃣ Set Up User Permissions and GPU Access
Configure proper permissions for GPU access:

# Add user to video group
    sudo usermod -aG video $USER

# Set GPU device permissions
    sudo chmod 660 /dev/dri/renderD128

# Install DKMS
    sudo apt install rocm-dkms

6️⃣ Configure Environment Variables
Add these environment variables to your shell:

# Add ROCm binaries to PATH
    export PATH=$PATH:/opt/rocm/bin:/opt/rocm/opencl/bin

# Configure library path
    export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/rocm/lib:/opt/rocm/opencl/lib

# Disable SDMA for stability
    export HSA_ENABLE_SDMA=0

# Set GPU version override
    export HSA_OVERRIDE_GFX_VERSION=9.0.0

7️⃣ Set Up TensileLibrary Symlink
Create necessary symlink for GPU support:

    sudo ln -s /opt/rocm-6.3.1/lib/rocblas/library/TensileLibrary_lazy_gfx900.dat /opt/rocm-6.3.1/lib/rocblas/library/TensileLibrary_lazy_gfx90c.dat

8️⃣ Build llama.cpp
Clone and compile llama.cpp with ROCm support:

# Clone repository
    git clone https://github.com/ggerganov/llama.cpp
    cd llama.cpp

# Install build dependencies
    sudo apt install build-essential cmake git rocm-dev

🔮 Configure and build

    sudo cmake -B build -DCMAKE_C_FLAGS="-march=znver2" -DGGML_HIP=ON -DAMDGPU_TARGETS=gfx900:xnack+
    sudo cmake --build build

Explanation of the Flags:

    -DCMAKE_C_FLAGS="-march=znver2"
        Purpose: Optimizes the build specifically for the AMD Zen 2 architecture (used in the Ryzen 7 5700U).

    -DGGML_HIP=ON
        Purpose: Enables HIP (Heterogeneous-Compute Interface for Portability) AMD support in llama.cpp.
        
    -DAMDGPU_TARGETS=gfx900:xnack+
        Purpose: Targets the specific GPU architecture of the Ryzen 7 5700U's Vega GPU.
        Components:
            gfx900: Specifies the GPU microarchitecture code for the Vega series used in the Ryzen 7 5700U APU.
            xnack+: Enables XNACK (eXtended Non-ACKnowledge), which allows the GPU to manage pageable memory more efficiently. This is important for systems where memory constraints and paging behavior could impact performance.    

# Check ROCm info
rocminfo

# Check OpenCL configuration
clinfo

# Monitor GPU usage
radeontop


🚀 What’s Next?

    Run Llama.cpp:
    Start using your AI inference server by running models:

./llama.cpp -m models/your-model.bin -p "Your prompt here"

🙌 Acknowledgments

Big thanks to:

    Llama.cpp for enabling lightweight LLM inference.
    The open-source community for ROCm and AMD GPU support.

This guide highlights the untapped potential of AMD Ryzen APUs for AI workloads. Dive in, experiment, and discover the future of low-cost, efficient AI inference. Let me know if anything is unclear or needs further refinement! 🚀


Benchmark Results for AMD Ryzen APU Inference with llama.cpp

This report highlights the performance of llama.cpp running on an AMD Ryzen 7 5700U APU using ROCm. Tests were conducted with the Llama-3.1-8B-Lexi-Uncensored-V2.i1-Q4_K_M model, and results are summarized to showcase the impact of GPU layer offloading, thread counts, and other parameters on tokens per second (t/s) for prompt processing (pp64) and text generation (tg128).
System Configuration

    Model: Llama-3.1-8B-Lexi-Uncensored-V2.i1-Q4_K_M
    Hardware: AMD Ryzen 7 5700U APU with integrated Radeon Graphics
    Software: llama.cpp with ROCm backend
    Model Size: 4.58 GiB, 8.03 billion parameters
    Batch Size: 512 tokens
    Prompt Tokens (pp64): 64 tokens
    Generated Tokens (tg128): 128 tokens
    Threads: Configurable (tested with 8, 15, and 16 threads)
    GPU Layers (ngl): Tested with 0 and 33 offloaded layers

🚀 Results Overview 

Benchmark Results for AMD Ryzen APU Inference with llama.cpp

This report highlights the performance of llama.cpp running on an AMD Ryzen 7 5700U APU using ROCm. Tests were conducted with the Llama-3.1-8B-Lexi-Uncensored-V2.i1-Q4_K_M model, and results are summarized to showcase the impact of GPU layer offloading, thread counts, and other parameters on tokens per second (t/s) for prompt processing (pp64) and text generation (tg128).
System Configuration

    Model: Llama-3.1-8B-Lexi-Uncensored-V2.i1-Q4_K_M
    Hardware: AMD Ryzen 7 5700U APU with integrated Radeon Graphics
    Software: llama.cpp with ROCm backend
    Model Size: 4.58 GiB, 8.03 billion parameters
    Batch Size: 512 tokens
    Prompt Tokens (pp64): 64 tokens
    Generated Tokens (tg128): 128 tokens
    Threads: Configurable (tested with 8, 15, and 16 threads)
    GPU Layers (ngl): Tested with 0 and 33 offloaded layers

GPU Layers (ngl)	Threads	Batch Size	        Prompt-Processing (t/s)	                Text Generation (t/s)
0	    15	             512	                18.54 ± 0.00                                 6.27 ± 0.00
33	    15	             512	                20.16 ± 0.00	                             6.82 ± 0.00
0	    8	             512	                18.51 ± 0.00	                             8.01 ± 0.00
33	    8	             512	                20.12 ± 0.00	                             6.76 ± 0.00
33	    16	             512	                20.22 ± 0.00	                             6.84 ± 0.00

GPU Offloading (ngl):

    Offloading 33 layers to the APU improves prompt processing speed from 18.54 t/s to 20.16 t/s (approx. 8.7% increase).
    Text generation speed sees a marginal improvement from 6.27 t/s to 6.82 t/s 

Threads:

    Increasing threads to 16 provides the best performance for both pp64 and tg128 tests.
    At 16 threads with 33 GPU layers, prompt processing achieves 20.22 t/s, and text generation reaches 6.84 t/s, the highest observed.

Why These Results Are Impressive

While the reported tokens per second may not sound groundbreaking compared to high-end discrete GPUs, the real achievement lies in completely offloading all 33 model layers to the integrated GPU. This offloading frees up 15 CPU threads, with only one CPU thread being utilized for llama.cpp, leaving the rest available for running additional workloads such as:

    Whisper.cpp for real-time transcription or speech processing.
    Other concurrent programs like data pre-processing, serving HTTP requests, or lightweight orchestration tasks.

This setup highlights the efficiency of AMD APUs, demonstrating how integrated GPUs can handle full AI inference pipelines while reserving CPU resources for other tasks. It's a testament to how low-power, cost-efficient hardware can be repurposed for multipurpose AI workloads.
Conclusion

The AMD Ryzen 7 5700U APU with ROCm delivers a versatile and energy-efficient solution for AI inference. By offloading all layers to the integrated GPU and leaving CPU resources free, this configuration exemplifies a practical and affordable approach for developers and researchers. It is ideal for building lightweight AI servers capable of running multiple processes simultaneously.
