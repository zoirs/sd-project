# Dockerfile to set up Stable Diffusion with Realistic models and ControlNet (MLSD)

# Use an official PyTorch image as a base
FROM nvidia/cuda:11.7.1-cudnn8-runtime-ubuntu20.04

# Set environment variables
ENV DEBIAN_FRONTEND=noninteractive
ENV PYTHONUNBUFFERED=1
ARG AUTH_TOKEN

# Install necessary libraries and tools
RUN apt-get update && \
    apt-get install -y software-properties-common && \
    add-apt-repository ppa:deadsnakes/ppa && \
    apt-get update && \
    apt-get install -y \
    python3.10 \
    python3.10-venv \
    python3.10-dev \
    python3-pip \
    git \
    wget \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Create an alternative temp directory for pip and set it as TMPDIR
RUN mkdir /install-tmp
RUN chmod -R 777 /install-tmp
ENV TMPDIR=/install-tmp

# Set working directory
WORKDIR /opt/stable-diffusion

# Clone Stable Diffusion WebUI repository
RUN git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui.git .
# RUN git pull origin master


# Install Python dependencies
RUN pip3 install --no-cache-dir -r requirements.txt

# Download Realistic model
RUN wget --header "Authorization: Bearer ${AUTH_TOKEN}" -P models/Stable-diffusion https://huggingface.co/SG161222/Realistic_Vision_V6.0_B1_noVAE/resolve/main/Realistic_Vision_V6.0_NV_B1.safetensors
# RUN wget -P models/Stable-diffusion https://huggingface.co/username/realistics_model/resolve/main/realistics_model.ckpt

# ${AUTH_TOKEN}
# Download ControlNet extension and MLSD model
RUN git clone https://github.com/Mikubill/sd-webui-controlnet.git extensions/sd-webui-controlnet
# RUN wget -P extensions/sd-webui-controlnet/models https://huggingface.co/lllyasviel/ControlNet/resolve/main/control_mlsd.pth
# RUN wget --header "Authorization: Bearer ${AUTH_TOKEN}" -P extensions/sd-webui-controlnet/models https://huggingface.co/lllyasviel/control_v11p_sd15_mlsd/resolve/main/diffusion_pytorch_model.safetensors
RUN wget --header "Authorization: Bearer ${AUTH_TOKEN}" -O extensions/sd-webui-controlnet/models/mlsd_model.safetensors https://huggingface.co/lllyasviel/control_v11p_sd15_mlsd/resolve/main/diffusion_pytorch_model.safetensors
RUN wget --header "Authorization: Bearer ${AUTH_TOKEN}" -O extensions/sd-webui-controlnet/models/canny_model.safetensors https://huggingface.co/lllyasviel/control_v11p_sd15_canny/resolve/main/diffusion_pytorch_model.safetensors
RUN wget --header "Authorization: Bearer ${AUTH_TOKEN}" -O extensions/sd-webui-controlnet/models/inpaint_model.safetensors https://huggingface.co/lllyasviel/control_v11p_sd15_inpaint/resolve/main/diffusion_pytorch_model.safetensors
RUN wget --header "Authorization: Bearer ${AUTH_TOKEN}" -O extensions/sd-webui-controlnet/models/lineart_model.safetensors https://huggingface.co/lllyasviel/control_v11p_sd15_lineart/resolve/main/diffusion_pytorch_model.safetensors
RUN wget --header "Authorization: Bearer ${AUTH_TOKEN}" -O extensions/sd-webui-controlnet/models/depth_model.safetensors https://huggingface.co/lllyasviel/control_v11f1p_sd15_depth/resolve/main/diffusion_pytorch_model.safetensors
RUN wget --header "Authorization: Bearer ${AUTH_TOKEN}" -O extensions/sd-webui-controlnet/models/openpose_model.safetensors https://huggingface.co/lllyasviel/control_v11p_sd15_openpose/resolve/main/diffusion_pytorch_model.safetensors

# Add a new user and switch to that user
RUN useradd -m stableuser && chown -R stableuser /opt/stable-diffusion
USER stableuser

# Expose port for web UI
EXPOSE 7860

# Set entrypoint to run WebUI with required parameters
CMD ["bash", "webui.sh", "--xformers", "--listen", "--enable-insecure-extension-access", "--api"]

