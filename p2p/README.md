## 📡 Phase 2 Blueprint: Off-Grid P2P Pager & Solar Mesh Network

While **AuraNet** is engineered for autonomous communication in early Martian settlements, its core architecture serves as an open blueprint for **resilient, off-grid, and low-power mesh networks on Earth**—anywhere cellular infrastructure is unavailable, overloaded, or undesirable.

### 🛠 The 3-Node Starter Topology
The initial P2P and multi-hop testbed consists of three uniform, `no_std` Rust-powered nodes (Raspberry Pi Pico + SX1262 LoRa):
* **2× Mobile E-Paper Pagers:** Ultra-low-power handheld terminals featuring 2.66" E-Paper displays, automatic message acknowledgments (`ACK`), and tactile quick-reply buttons.
* **1× Autonomous Solar Relay:** A headless, solar-powered repeater node mounted at an elevated position to extend range, bridge urban or natural obstacles, and act as a fixed anchor for **GPS-free location estimation** (via RSSI and breadcrumb path tracking).

---

### 🌍 Use Cases: From Mars to Earth

Because the underlying Rust mesh protocol (`auranet-proto`) is lightweight, hardware-agnostic, and completely independent of the internet or cellular towers, the exact same 3-node setup adapts to a wide range of real-world scenarios:

#### 🚀 Space & Analog Missions
* **Mars Habitat & EVA Pagers:** Reliable, wrist- or suit-mounted text terminals for astronauts on extravehicular activities (EVAs) to receive mission updates, hazard alerts, and send quick confirmations without relying on orbital satellites.
* **Earth-Based Analog Missions & Field Research:** Lightweight basecamp-to-team communication for geologists, cavers, and researchers operating in remote deserts, mountains, or subterranean environments.

#### 🛡️ Safety, Resilience & Off-Grid Security
* **Neighborhood Crisis & Blackout Mesh:** A local, solar-backed emergency network allowing families and neighbors to coordinate and share status updates when power grids and cell towers fail.
* **Lone-Worker & Outdoor "Dead-Man's Switch":** Automated periodic heartbeats between mobile pagers and the relay—triggering immediate alerts with last-known signal paths if a connection drops or motion ceases.
* **Decentralized Swarm Alarms (e.g., Bicycle Security):** Tamper-triggered nodes that broadcast silent alerts across nearby peer nodes without requiring SIM cards or cloud subscriptions.

#### 🌱 Nature, Gardening & Citizen Science
* **Autonomous Greenhouse & Garden Telemetry:** Equipping the solar relay with soil moisture, frost, or temperature sensors to send calm, daily status summaries straight to a kitchen E-Paper pager.
* **Wildlife & Environmental Monitoring:** Deploying solar sensor nodes deep in forests or wetlands for non-intrusive acoustic or climate monitoring, relaying findings to researchers at basecamp.

#### 🎨 Culture, Community & "Quiet Tech"
* **Interactive Art & Urban Scavenger Hunts:** Location-aware storytelling and city games where clues automatically unlock on a player's E-Paper display based on proximity (`RSSI`) to hidden solar relays.
* **Hyper-Local Kiez Bulletin Boards:** A distraction-free, zero-tracking community network for sharing tools, surplus food, or local meetups within a neighborhood block.
* **Sustainable, Repairable Venue Pagers:** A long-lasting, open-source alternative to disposable restaurant/foodtruck buzzers—featuring readable E-Paper menus, order status updates, and two-way table requests.
