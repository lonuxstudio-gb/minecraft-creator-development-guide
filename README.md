# 🎮 Minecraft Creator Development Guide

> Hướng dẫn chi tiết cho các nhà sáng tạo Minecraft từ cơ bản đến chuyên nghiệp

---

## 📚 Mục Lục

1. [Giới Thiệu](#giới-thiệu)
2. [Chuẩn Bị Môi Trường](#chuẩn-bị-môi-trường)
3. [Cơ Bản](#cơ-bản)
4. [Trung Cấp](#trung-cấp)
5. [Nâng Cao](#nâng-cao)
6. [Chuyên Nghiệp](#chuyên-nghiệp)
7. [Khắc Phục Lỗi](#khắc-phục-lỗi)

---

## 🎯 Giới Thiệu

Cuốn hướng dẫn này cung cấp kiến thức toàn diện về:
- Phát triển **Add-on** cho Minecraft
- Tạo **Resource Packs** và **Behavior Packs**
- Sử dụng **Minecraft Command Language**
- Lập trình với **JavaScript/TypeScript** cho Minecraft Scripting API
- Tối ưu hóa và triển khai

---

## 💻 Chuẩn Bị Môi Trường

### 🐧 Linux (Ubuntu/Debian)

```bash
# Cập nhật hệ thống
sudo apt update && sudo apt upgrade -y

# Cài đặt Node.js
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs

# Cài đặt Git
sudo apt install -y git

# Cài đặt VS Code
sudo apt install -y code

# Cài đặt Termux (nếu dùng Android)
# Download từ F-Droid hoặc Play Store
```

**Phần mềm cần thiết:**
- **Node.js** (v18+): Runtime JavaScript
- **VS Code**: Editor chính
- **Git**: Version control
- **Termux** (Android): Terminal trên mobile
- **vim/nano**: Editor dòng lệnh

---

### 🪟 Windows

```powershell
# Cài đặt Node.js (từ https://nodejs.org)
# Sau đó chạy:
node --version
npm --version

# Cài đặt Git
# Download từ https://git-scm.com

# Cài đặt VS Code
# Download từ https://code.visualstudio.com
```

**Phần mềm cần thiết:**
- **Node.js**: Runtime JavaScript
- **VS Code**: Editor chính
- **Git Bash**: Terminal
- **PowerShell**: Command line

---

### 🍎 macOS

```bash
# Cài đặt Homebrew (nếu chưa có)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Cài đặt Node.js
brew install node

# Cài đặt Git
brew install git

# Cài đặt VS Code
brew install --cask visual-studio-code
```

**Phần mềm cần thiết:**
- **Node.js**: Runtime JavaScript
- **VS Code**: Editor chính
- **Git**: Version control
- **Terminal**: Command line

---

### 📱 Mobile (Android - Termux)

```bash
# Cài đặt Termux từ F-Droid

# Trong Termux:
pkg update && pkg upgrade -y
pkg install -y nodejs git vim nano

# Kiểm tra phiên bản
node --version
npm --version
```

**Phần mềm cần thiết:**
- **Termux**: Terminal emulator
- **Node.js**: Runtime JavaScript
- **vim/nano**: Text editor

---

## 📖 Cơ Bản

### 1️⃣ Hiểu Cấu Trúc Add-on

```
my-addon/
├── pack_manifest.json       # Metadata của pack
├── behaviors/               # Behavior Pack
│   ├── entities/
│   ├── functions/
│   └── loot_tables/
└── resources/               # Resource Pack
    ├── textures/
    ├── models/
    └── sounds/
```

### 2️⃣ Tạo Pack Manifest

**pack_manifest.json:**
```json
{
  "format_version": 2,
  "header": {
    "name": "My First Add-on",
    "description": "A simple Minecraft add-on",
    "uuid": "12345678-1234-1234-1234-123456789012",
    "version": [1, 0, 0],
    "min_engine_version": [1, 20, 0]
  },
  "modules": [
    {
      "type": "resources",
      "uuid": "87654321-4321-4321-4321-210987654321",
      "version": [1, 0, 0]
    },
    {
      "type": "data",
      "uuid": "11111111-2222-3333-4444-555555555555",
      "version": [1, 0, 0]
    }
  ]
}
```

### 3️⃣ Tạo Entity Đơn Giản

**behaviors/entities/my_entity.json:**
```json
{
  "format_version": "1.20.0",
  "minecraft:entity": {
    "description": {
      "identifier": "myaddon:my_entity",
      "is_spawnable": true,
      "is_summonable": true,
      "is_experimental": false
    },
    "component_groups": {
      "minecraft:foo": {}
    },
    "components": {
      "minecraft:type_family": {
        "family": ["mob"]
      },
      "minecraft:health": {
        "value": 20
      }
    }
  }
}
```

---

## 📘 Trung Cấp

### 2️⃣ Command Scripting

**Ví dụ Command đơn giản:**
```mcfunction
# Khi player join
execute as @a[tag=!joined] run function initialize_player

# Gọi function
/function spawn_structure
```

**Function (functions/spawn_structure.mcfunction):**
```mcfunction
fill ~ ~1 ~ ~10 ~10 ~10 stone
setblock ~ ~ ~ chest{Items:[{Slot:0b,id:"minecraft:diamond_block",Count:64b}]}
```

### 3️⃣ Behavior Packs - NPC Interaction

```json
{
  "format_version": "1.20.0",
  "minecraft:entity": {
    "description": {
      "identifier": "myaddon:custom_npc",
      "is_summonable": true
    },
    "components": {
      "minecraft:npc": {
        "name": "Friendly NPC"
      },
      "minecraft:interact": {
        "interactions": [
          {
            "on_interact": {
              "filters": [
                {
                  "test": "is_family",
                  "subject": "other",
                  "value": "player"
                }
              ]
            },
            "use_item": false,
            "swing": true,
            "play_sound": "mob.villager.yes"
          }
        ]
      }
    }
  }
}
```

---

## 📗 Nâng Cao

### 4️⃣ Scripting API (JavaScript)

**index.js:**
```javascript
import { world, system } from "@minecraft/server";

// Listen to ticks
system.afterEvents.scriptEventReceive.subscribe((event) => {
  if (event.id === "custom:my_event") {
    world.sendMessage("Event triggered!");
  }
});

// Get player and modify
world.afterEvents.playerSpawn.subscribe((event) => {
  const player = event.player;
  player.tell("Welcome to my add-on!");
});
```

### 5️⃣ Loot Tables

**loot_tables/blocks/custom_block.json:**
```json
{
  "pools": [
    {
      "rolls": 1,
      "entries": [
        {
          "type": "item",
          "name": "minecraft:diamond",
          "weight": 5
        },
        {
          "type": "item",
          "name": "minecraft:emerald",
          "weight": 2
        }
      ],
      "conditions": [
        {
          "condition": "match_tool",
          "predicate": {
            "item": "minecraft:diamond_pickaxe"
          }
        }
      ]
    }
  ]
}
```

---

## 📕 Chuyên Nghiệp

### 6️⃣ Advanced Entity Behavior

**Complex entity với animation:**
```json
{
  "format_version": "1.20.0",
  "minecraft:entity": {
    "description": {
      "identifier": "myaddon:boss_entity",
      "is_summonable": true,
      "animations": {
        "idle": "animation.boss.idle",
        "attack": "animation.boss.attack",
        "death": "animation.boss.death"
      },
      "scripts": {
        "animate": [
          "idle",
          "attack"
        ]
      }
    },
    "components": {
      "minecraft:health": {
        "value": 100,
        "max": 100
      },
      "minecraft:attack": {
        "damage": 10
      },
      "minecraft:behavior.melee_attack": {
        "priority": 4,
        "speed_multiplier": 1.0
      }
    },
    "events": {
      "minecraft:entity_spawned": {
        "add": {
          "component_groups": ["boss_mode"]
        }
      }
    }
  }
}
```

### 7️⃣ Resource Pack Texturing

**pack.mcmeta:**
```json
{
  "pack": {
    "pack_format": 15,
    "description": "My Resource Pack"
  }
}
```

**Texture mapping (textures/item_texture.json):**
```json
{
  "resource_pack_name": "My Resource Pack",
  "texture_name": "atlas.items",
  "texture_data": {
    "custom_item": {
      "textures": "textures/items/custom_item"
    }
  }
}
```

### 8️⃣ Optimization Tips

**Tối ưu hóa hiệu suất:**

```mcfunction
# Chỉ execute với player gần
execute as @a[distance=..32] run function check_player

# Dùng scoreboards thay vì NBT
scoreboard objectives add health dummy
execute as @e run scoreboard players set @s health 20

# Limit entities
execute if entity @e[type=myaddon:custom_mob,c=100] run kill @e[type=myaddon:custom_mob,c=1,sort=furthest]
```

---

## 🔧 Khắc Phục Lỗi

### ❌ Lỗi Phổ Biến & Giải Pháp

| Lỗi | Nguyên Nhân | Giải Pháp |
|-----|-----------|----------|
| `Unknown block/item` | Identifier sai | Kiểm tra `namespace:name` |
| `JSON Parse Error` | Syntax JSON sai | Dùng JSON validator |
| `Entity not spawning` | `is_summonable: false` | Chỉnh thành `true` |
| `Function not found` | Path sai | Kiểm tra đường dẫn file |
| `Permission denied` | Quyền không đủ | Chạy lệnh với OP |

### 📋 Checklist Debug

```bash
# 1. Kiểm tra manifest
cat pack_manifest.json | python3 -m json.tool

# 2. Kiểm tra file tồn tại
ls -la behaviors/entities/

# 3. Xem logs
tail -f minecraft_logs.txt

# 4. Test entity đơn giản
/summon myaddon:my_entity

# 5. Kiểm tra scoreboard
/scoreboard objectives list
```

---

## 🚀 Deployment

### Cách Triển Khai Add-on

**Trên Windows/Mac:**
1. Vào thư mục `saves/` hoặc `world_templates/`
2. Copy folder add-on vào đây
3. Khởi động Minecraft và chọn world có add-on

**Trên Android (Termux):**
```bash
# Sao chép vào thư mục Minecraft
cp -r my-addon ~/.local/share/mcpelauncher/data/

# Hoặc export thành .mcaddon
zip -r my-addon.mcaddon my-addon/
```

**Trên Server:**
```bash
# Paste vào server packs folder
server/packs/my-addon/

# Restart server
./start.sh
```

---

## 📚 Tài Liệu Tham Khảo

- [Official Minecraft Wiki](https://minecraft.wiki)
- [Minecraft Add-on Documentation](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/)
- [Scripting API Docs](https://learn.microsoft.com/en-us/minecraft/creator/scriptapi/)

---

## 🤝 Contribute

Nếu bạn có góp ý hoặc muốn cải thiện guide này, hãy tạo **Pull Request**!

---

## 📄 License

MIT License - Tự do sử dụng cho mục đích cá nhân

---

**Made with ❤️ by lonuxstudio**
