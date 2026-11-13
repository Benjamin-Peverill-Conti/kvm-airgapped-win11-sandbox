Provisioning an air-gapped Windows 11 Pro VM on Linux KVM/QEMU with emulated TPM 2.0 (swtpm), zero virtual NICs, and offline OOBE local account deployment.

## 📌 Project Overview

This repository documents the architecture and deployment of an air-gapped **Windows 11 Pro** virtual machine (`Version 10.0.26300`) running on a Debian/Ubuntu-based Linux host (Pop!_OS) via **KVM/QEMU** and `virt-manager`. The environment is engineered for isolated testing and golden-image preparation with emulated hardware security (**TPM 2.0**), zero network interfaces, and a local offline user profile.

## 🏗️ Hypervisor & Security Architecture

- **Host Hypervisor:** Ubuntu/Debian-based Linux host running **QEMU/KVM** managed via `libvirt` and `virt-manager`.
- **Hardware-Level TPM 2.0 Emulation:** Configured an emulated **TPM v2.0** device (`swtpm`) alongside UEFI (`OVMF`) firmware to satisfy Windows 11 cryptographic boot and BitLocker readiness requirements without exposing physical host TPM keys.
- **Strict Air-Gap Isolation (No Virtual NIC):** Intentionally removed all virtual network interface cards (`NIC`) prior to installation to guarantee zero ingress or egress network traffic during initial provisioning and offline validation.

## 🛠️ Offline OOBE Provisioning & Troubleshooting

Modern Windows 11 builds (`26300+`) deprecate and remove the legacy `oobe\bypassnro` batch script, blocking standard offline setups when no network adapter is attached.

1. **Launched Elevated OOBE Command Prompt:**
   Pressed `Shift + F10` during the Out-of-Box Experience (OOBE) network prompt to access `C:\Windows\System32\cmd.exe`.

2. **Verified Deprecation of Legacy Bypass Script:**
   Confirmed that `oobeBypassNRO.cmd` is absent in build `10.0.26300.9457` (`'oobeBypassNRO.cmd' is not recognized as an internal or external command`).

3. **Provisioned Local Offline Administrator Account:**
   Configured the registry / local user policy directly via CLI (`reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\OOBE /v BypassNRO /t REG_DWORD /d 1 /f` or `start ms-cxh:localonly`) to complete OOBE setup without internet connectivity or Microsoft cloud account binding.

## ✅ Verification & Outcome

- **Offline Desktop Deployed:** Successfully booted into a clean Windows 11 Pro desktop environment with an active local account and zero network interfaces attached.
- **Reproducible Golden Image:** Established a clean, snapshot-ready baseline VM (`Btrfs` / `qcow2`) suitable for isolated testing or controlled attachment to segmented VLANs and WireGuard tunnels later.

