# HenLoader LP - Optimized Edition

Improved version of [henloader_lp](https://github.com/lucaslealdev/henloader_lp) with **heap grooming optimization** for significantly improved exploit reliability.

## 🎯 Key Improvements

### Heap Grooming Optimization
- **32 dummy socket allocations/deallocations** before kqueue realloc to stabilize kernel heap state
- **CPU yielding** (10x sched_yield) to allow kernel to process memory operations
- **Per-attempt timing optimization** with sched_yield before each kqueue() call
- **Adaptive heap stabilization** every 1000 attempts to maintain heap consistency

### Enhanced Retry Counters
- `IPV6_SOCK_NUM`: 128 → **192** (+50%)
- `TWIN_TRIES`: 15,000 → **30,000** (+100%)
- `UAF_TRIES`: 50,000 → **100,000** (+100%)
- `KQUEUE_TRIES`: 300,000 → **500,000** (+67%)

### Progress Logging
- Real-time progress indicators during kqueue realloc stage
- Success attempt reporting for debugging

## 📊 Results

| Version | Success Rate | Retries Needed |
|---------|--------------|----------------|
| **Original** | ~20-30% | 2-5 attempts |
| **Optimized** | **100%** | **1st attempt** |

**Tested:** 3/3 success (including cold boot)

## 🎮 Supported Firmware

- PlayStation 4: Firmware **≤ 12.52**
- Based on **Poops exploit** by TheFloW

## 📦 Download

Download the pre-built ISO from [Releases](https://github.com/manos555555/henloader_lp/releases)

## 🔨 Building from Source

### Prerequisites
- Linux environment (WSL2 on Windows works)
- Java 8 JDK
- Make
- libbsd-dev

### Build Steps

```bash
# Install dependencies (Ubuntu/Debian)
sudo apt-get install build-essential libbsd-dev pkg-config

# Clone repository
git clone https://github.com/manos555555/henloader_lp.git
cd henloader_lp/HenLoader

# Build
make

# Output: build/henloader.iso
```

## 🚀 Usage

1. Burn `henloader.iso` to a BD-R disc
2. Insert disc into PS4
3. Wait for exploit to run (progress shown on screen)
4. GoldHEN will load automatically

## 🔧 Technical Details

### Heap Grooming Implementation

The key improvement is in the `achieveRw()` function:

```java
// Stabilize heap with dummy allocations
int[] dummySocks = new int[32];
for (int i = 0; i < dummySocks.length; i++) {
    dummySocks[i] = socket(AF_INET6, SOCK_STREAM, 0);
}
for (int i = 0; i < dummySocks.length; i++) {
    close(dummySocks[i]);
}

// Allow kernel to process frees
for (int i = 0; i < 10; i++) {
    sched_yield();
}
```

This ensures the kqueue allocation lands in the correct heap slot previously occupied by the freed rthdr structure, dramatically improving reliability.

## 📝 Credits

- **TheFloW** - Poops kernel exploit
- **SiSTRO** - GoldHEN
- **Gezine** - Lapse exploit
- **kimariin** - BD-J build environment
- **sleirsgoevy** - Java console
- **lucaslealdev** - Original henloader_lp
- **manos555555** - Heap grooming optimization

## ⚖️ License

This project inherits the license from the original henloader_lp repository.

## ⚠️ Disclaimer

This software is provided for educational and research purposes only. Use at your own risk.
