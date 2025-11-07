# Visual Modeller Enhancement - Detailed Implementation Plan

**Document Date:** 2025-11-07
**Version:** 1.0
**Status:** Ready for Implementation
**Estimated Duration:** 4-6 weeks for Phase 1

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [Current State Assessment](#current-state-assessment)
3. [Phase 1: Core UX Improvements](#phase-1-core-ux-improvements)
4. [Phase 2: Advanced Properties & Configuration](#phase-2-advanced-properties--configuration)
5. [Phase 3: Collaboration & Enterprise Features](#phase-3-collaboration--enterprise-features)
6. [Phase 4: Advanced Patterns & Intelligence](#phase-4-advanced-patterns--intelligence)
7. [Technical Architecture](#technical-architecture)
8. [Testing Strategy](#testing-strategy)
9. [Deployment Plan](#deployment-plan)
10. [Risk Management](#risk-management)

---

## Executive Summary

### What We Have Now (Completed ✅)
- ✅ 15 system types in palette (expanded from 5)
- ✅ 13 integration patterns (expanded from 6)
- ✅ Comprehensive properties panel with error handling, security, SLA
- ✅ Real-time validation with error/warning indicators
- ✅ Bug fixes (edge alignment, clear all functionality)
- ✅ Bezier curves, touch support, zoom/pan/undo/redo

### What We Need Next (Priority Order)

**Phase 1: Core UX Improvements** (2 weeks)
- Searchable, categorized palette with favorites
- Multi-select and bulk operations
- Keyboard shortcuts and accessibility
- Grid/alignment guides
- Mini-map navigation

**Phase 2: Advanced Properties** (2 weeks)
- Transformation builder (field mapping)
- Schema editor (JSON/XML/Avro)
- Pattern-specific configuration forms
- Connection testing & preview

**Phase 3: Collaboration** (1-2 weeks)
- Git integration (save/load/version control)
- Export improvements (PNG, PDF, documentation)
- Comments & annotations
- Change history visualization

**Phase 4: Advanced Features** (Ongoing)
- Template library with industry patterns
- Cost estimation
- AI-powered suggestions
- Compliance checking

---

## Current State Assessment

### Completed Features ✅

#### Palette System
```javascript
// Current Implementation
Systems: 15 types (Application, Database, Service, External, Legacy,
         API Gateway, Message Broker, Cache, File Storage, Data Warehouse,
         ETL, Container, Serverless, Auth, Monitoring, CDN, Analytics,
         CRM, ERP, HR)

Integration Patterns: 13 types (REST API, SOAP API, GraphQL, gRPC, WebSocket,
                      Event Stream, Messaging, File Transfer, Batch,
                      Database Sync, EDI, Webhook, Custom)
```

#### Properties Panel
```javascript
// Current Sections
1. Basic Information (name, type, description, version, owner, environment)
2. Connection Configuration (URLs, timeout)
3. Error Handling (retry, backoff, DLQ, alerting)
4. Security (auth types, encryption)
5. Performance & SLA (latency, throughput, availability)
6. Metadata (owner, tags, documentation URL)
```

#### Validation System
```javascript
// Current Validation
- Real-time indicator (✅ Valid, ❌ Errors, ⚠️ Warnings)
- Comprehensive checks (required fields, references, security, error handling)
- Modal results display with detailed error/warning messages
- Auto-updates on every change
```

### Gaps & Improvement Areas

#### 1. Palette UX
```
Current: Simple list, no organization
Needed:
  - Search/filter functionality
  - Category collapsing/expanding
  - Favorites system
  - Recently used tracking
  - Drag preview
```

#### 2. Canvas Operations
```
Current: Single node operations, basic drag
Needed:
  - Multi-select (Shift+Click, Ctrl+Click, Drag-select box)
  - Bulk operations (move, delete, duplicate, style)
  - Alignment tools (distribute, align edges)
  - Grid snapping
  - Mini-map for large diagrams
```

#### 3. Properties Panel
```
Current: Generic forms for all patterns
Needed:
  - Pattern-specific forms (REST shows endpoints, Kafka shows topics)
  - Transformation builder (visual field mapping)
  - Schema editor with validation
  - Test connection button
  - Code generation preview
```

#### 4. Collaboration
```
Current: Local JSON export only
Needed:
  - Git integration (push, pull, branches)
  - Visual diff for changes
  - Comments on nodes/connections
  - Export to image/PDF with annotations
  - Auto-generated documentation
```

---

## Phase 1: Core UX Improvements

**Duration:** 2 weeks
**Priority:** P0 (Critical)
**Goal:** Make the tool significantly more productive for daily use

### 1.1 Searchable & Categorized Palette

#### Implementation Details

**File:** `visual-designer.html` (lines 915-1170)

**Current Structure:**
```html
<div class="palette">
  <div class="section">
    <h3>🖥️ Systems</h3>
    <div class="palette-item">...</div>
    ...
  </div>
  <div class="section">
    <h3>🔌 Integration Patterns</h3>
    <div class="palette-item">...</div>
    ...
  </div>
</div>
```

**New Structure:**
```html
<div class="palette">
  <!-- Search Bar -->
  <div class="palette-search">
    <input type="text"
           id="paletteSearch"
           placeholder="🔍 Search components..."
           oninput="filterPalette(this.value)">
    <button onclick="clearPaletteSearch()">✕</button>
  </div>

  <!-- Filter Tabs -->
  <div class="palette-tabs">
    <button class="tab active" onclick="showPaletteTab('all')">All</button>
    <button class="tab" onclick="showPaletteTab('favorites')">★ Favorites</button>
    <button class="tab" onclick="showPaletteTab('recent')">🕐 Recent</button>
  </div>

  <!-- Favorites Section (Initially Hidden) -->
  <div class="palette-section" id="favoritesSection" style="display: none;">
    <h3>★ Favorites</h3>
    <div id="favoritesList" class="palette-items"></div>
    <p class="empty-message" style="display: none;">
      No favorites yet. Click the ★ on any item to add it.
    </p>
  </div>

  <!-- Recently Used (Initially Hidden) -->
  <div class="palette-section" id="recentSection" style="display: none;">
    <h3>🕐 Recently Used</h3>
    <div id="recentList" class="palette-items"></div>
  </div>

  <!-- Categorized Systems -->
  <div class="palette-section" id="systemsSection">
    <h3 class="collapsible" onclick="toggleSection('systems')">
      <span class="collapse-icon">▼</span> 🖥️ Systems (15)
    </h3>
    <div class="palette-items" id="systemsItems">
      <!-- Existing system items with star button -->
      <div class="palette-item" draggable="true" data-type="system"
           data-subtype="application" data-search-terms="application business app">
        <button class="favorite-btn" onclick="toggleFavorite(this, 'system-application')">☆</button>
        <span class="palette-icon">💼</span>
        <div class="palette-label">
          <div class="palette-label-title">Application</div>
          <div class="palette-label-desc">Business app</div>
        </div>
      </div>
      <!-- ... repeat for all systems ... -->
    </div>
  </div>

  <!-- Categorized Patterns -->
  <div class="palette-section" id="patternsSection">
    <h3 class="collapsible" onclick="toggleSection('patterns')">
      <span class="collapse-icon">▼</span> 🔌 Integration Patterns (13)
    </h3>
    <div class="palette-items" id="patternsItems">
      <!-- Pattern items with star button -->
    </div>
  </div>

  <!-- Category: API & Web -->
  <div class="palette-category">
    <h4 class="collapsible" onclick="toggleSection('api-web')">
      <span class="collapse-icon">▼</span> 🌐 API & Web (5)
    </h4>
    <div class="palette-items" id="apiWebItems" style="display: none;">
      <div class="palette-item-small">REST API, GraphQL, SOAP, gRPC, WebSocket</div>
    </div>
  </div>

  <!-- Category: Messaging & Events -->
  <div class="palette-category">
    <h4 class="collapsible" onclick="toggleSection('messaging')">
      <span class="collapse-icon">▼</span> 📨 Messaging & Events (3)
    </h4>
    <div class="palette-items" id="messagingItems" style="display: none;">
      <div class="palette-item-small">Event Stream, Messaging, Webhook</div>
    </div>
  </div>

  <!-- Category: Data Integration -->
  <div class="palette-category">
    <h4 class="collapsible" onclick="toggleSection('data')">
      <span class="collapse-icon">▼</span> 🗄️ Data Integration (3)
    </h4>
    <div class="palette-items" id="dataItems" style="display: none;">
      <div class="palette-item-small">Database Sync, File Transfer, Batch</div>
    </div>
  </div>
</div>
```

**New CSS Styles:**
```css
/* Palette Search */
.palette-search {
    padding: 0.5rem;
    border-bottom: 1px solid var(--border);
    position: sticky;
    top: 0;
    background: white;
    z-index: 10;
}

.palette-search input {
    width: 100%;
    padding: 0.5rem 2rem 0.5rem 0.5rem;
    border: 1px solid var(--border);
    border-radius: 0.375rem;
    font-size: 0.875rem;
}

.palette-search input:focus {
    outline: none;
    border-color: var(--purple);
    box-shadow: 0 0 0 3px rgba(139, 92, 246, 0.1);
}

.palette-search button {
    position: absolute;
    right: 1rem;
    top: 50%;
    transform: translateY(-50%);
    background: none;
    border: none;
    cursor: pointer;
    padding: 0.25rem;
    color: var(--text-secondary);
}

/* Palette Tabs */
.palette-tabs {
    display: flex;
    gap: 0.25rem;
    padding: 0.5rem;
    border-bottom: 1px solid var(--border);
    background: var(--bg-secondary);
}

.palette-tabs .tab {
    flex: 1;
    padding: 0.5rem;
    border: none;
    background: white;
    border-radius: 0.25rem;
    font-size: 0.75rem;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.2s;
}

.palette-tabs .tab:hover {
    background: var(--bg-hover);
}

.palette-tabs .tab.active {
    background: var(--purple);
    color: white;
}

/* Collapsible Sections */
.collapsible {
    cursor: pointer;
    user-select: none;
    display: flex;
    align-items: center;
    gap: 0.5rem;
}

.collapse-icon {
    transition: transform 0.2s;
    display: inline-block;
}

.collapsible.collapsed .collapse-icon {
    transform: rotate(-90deg);
}

/* Favorite Button */
.favorite-btn {
    position: absolute;
    top: 0.25rem;
    right: 0.25rem;
    background: white;
    border: 1px solid var(--border);
    border-radius: 50%;
    width: 1.5rem;
    height: 1.5rem;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    opacity: 0;
    transition: all 0.2s;
    z-index: 5;
}

.palette-item:hover .favorite-btn {
    opacity: 1;
}

.favorite-btn.active {
    opacity: 1;
    background: var(--purple);
    color: white;
    border-color: var(--purple);
}

.favorite-btn:hover {
    transform: scale(1.1);
}

/* Empty Message */
.empty-message {
    text-align: center;
    color: var(--text-secondary);
    font-size: 0.75rem;
    padding: 1rem;
    font-style: italic;
}

/* Search Highlight */
.palette-item.search-match {
    background: rgba(139, 92, 246, 0.1);
    border-color: var(--purple);
}

.palette-item.search-hidden {
    display: none;
}
```

**New JavaScript Functions:**
```javascript
// ============ Palette Search & Filter ============
let paletteState = {
    favorites: JSON.parse(localStorage.getItem('paletteFavorites') || '[]'),
    recent: JSON.parse(localStorage.getItem('paletteRecent') || '[]'),
    currentTab: 'all'
};

function filterPalette(searchTerm) {
    const term = searchTerm.toLowerCase().trim();
    const items = document.querySelectorAll('.palette-item');

    let matchCount = 0;

    items.forEach(item => {
        if (!term) {
            item.classList.remove('search-hidden', 'search-match');
            return;
        }

        // Get search terms from data attribute and text content
        const searchTerms = item.getAttribute('data-search-terms') || '';
        const title = item.querySelector('.palette-label-title')?.textContent || '';
        const desc = item.querySelector('.palette-label-desc')?.textContent || '';

        const searchableText = `${searchTerms} ${title} ${desc}`.toLowerCase();

        if (searchableText.includes(term)) {
            item.classList.remove('search-hidden');
            item.classList.add('search-match');
            matchCount++;
        } else {
            item.classList.add('search-hidden');
            item.classList.remove('search-match');
        }
    });

    // Show "no results" message if needed
    const sections = document.querySelectorAll('.palette-section');
    sections.forEach(section => {
        const visibleItems = section.querySelectorAll('.palette-item:not(.search-hidden)');
        const noResults = section.querySelector('.no-results');

        if (visibleItems.length === 0 && term) {
            if (!noResults) {
                const msg = document.createElement('p');
                msg.className = 'no-results empty-message';
                msg.textContent = 'No matching items';
                section.querySelector('.palette-items').appendChild(msg);
            }
        } else if (noResults) {
            noResults.remove();
        }
    });
}

function clearPaletteSearch() {
    document.getElementById('paletteSearch').value = '';
    filterPalette('');
}

function showPaletteTab(tab) {
    paletteState.currentTab = tab;

    // Update active tab button
    document.querySelectorAll('.palette-tabs .tab').forEach(btn => {
        btn.classList.toggle('active', btn.textContent.includes(tab === 'favorites' ? 'Favorites' : tab === 'recent' ? 'Recent' : 'All'));
    });

    // Show/hide sections
    document.getElementById('favoritesSection').style.display = tab === 'favorites' ? 'block' : 'none';
    document.getElementById('recentSection').style.display = tab === 'recent' ? 'block' : 'none';
    document.getElementById('systemsSection').style.display = tab === 'all' ? 'block' : 'none';
    document.getElementById('patternsSection').style.display = tab === 'all' ? 'block' : 'none';

    // Update favorites/recent lists
    if (tab === 'favorites') {
        updateFavoritesList();
    } else if (tab === 'recent') {
        updateRecentList();
    }
}

function toggleFavorite(btn, itemId) {
    const isFavorite = paletteState.favorites.includes(itemId);

    if (isFavorite) {
        paletteState.favorites = paletteState.favorites.filter(id => id !== itemId);
        btn.textContent = '☆';
        btn.classList.remove('active');
    } else {
        paletteState.favorites.push(itemId);
        btn.textContent = '★';
        btn.classList.add('active');
    }

    // Save to localStorage
    localStorage.setItem('paletteFavorites', JSON.stringify(paletteState.favorites));

    // Update favorites list if visible
    if (paletteState.currentTab === 'favorites') {
        updateFavoritesList();
    }
}

function updateFavoritesList() {
    const list = document.getElementById('favoritesList');
    const emptyMsg = document.querySelector('#favoritesSection .empty-message');

    if (paletteState.favorites.length === 0) {
        list.innerHTML = '';
        emptyMsg.style.display = 'block';
        return;
    }

    emptyMsg.style.display = 'none';
    list.innerHTML = '';

    paletteState.favorites.forEach(itemId => {
        const [type, subtype] = itemId.split('-');
        const original = document.querySelector(`[data-type="${type}"][data-subtype="${subtype}"]`);
        if (original) {
            const clone = original.cloneNode(true);
            list.appendChild(clone);
        }
    });
}

function trackRecentUse(type, subtype) {
    const itemId = `${type}-${subtype}`;

    // Remove if already in recent
    paletteState.recent = paletteState.recent.filter(id => id !== itemId);

    // Add to beginning
    paletteState.recent.unshift(itemId);

    // Keep only last 10
    paletteState.recent = paletteState.recent.slice(0, 10);

    // Save to localStorage
    localStorage.setItem('paletteRecent', JSON.stringify(paletteState.recent));
}

function updateRecentList() {
    const list = document.getElementById('recentList');

    if (paletteState.recent.length === 0) {
        list.innerHTML = '<p class="empty-message">No recently used items</p>';
        return;
    }

    list.innerHTML = '';

    paletteState.recent.forEach(itemId => {
        const [type, subtype] = itemId.split('-');
        const original = document.querySelector(`[data-type="${type}"][data-subtype="${subtype}"]`);
        if (original) {
            const clone = original.cloneNode(true);
            list.appendChild(clone);
        }
    });
}

function toggleSection(sectionId) {
    const section = document.getElementById(`${sectionId}Items`);
    const header = event.currentTarget;

    if (section.style.display === 'none') {
        section.style.display = 'block';
        header.classList.remove('collapsed');
    } else {
        section.style.display = 'none';
        header.classList.add('collapsed');
    }

    // Save state
    localStorage.setItem(`section-${sectionId}`, section.style.display);
}

// Initialize palette on load
function initializePalette() {
    // Restore section states
    ['systems', 'patterns', 'api-web', 'messaging', 'data'].forEach(sectionId => {
        const state = localStorage.getItem(`section-${sectionId}`);
        if (state) {
            const section = document.getElementById(`${sectionId}Items`);
            if (section) {
                section.style.display = state;
            }
        }
    });

    // Update favorite buttons
    document.querySelectorAll('.palette-item').forEach(item => {
        const type = item.getAttribute('data-type');
        const subtype = item.getAttribute('data-subtype');
        const itemId = `${type}-${subtype}`;
        const btn = item.querySelector('.favorite-btn');

        if (btn && paletteState.favorites.includes(itemId)) {
            btn.textContent = '★';
            btn.classList.add('active');
        }
    });
}

// Call on page load
document.addEventListener('DOMContentLoaded', initializePalette);
```

**Integration Points:**
1. Update existing `onDragStart` to call `trackRecentUse()`
2. Update all palette items to include favorite buttons
3. Add search terms data attributes for better search
4. Test search performance with 50+ items

**Acceptance Criteria:**
- ✅ Search filters items in real-time (< 50ms)
- ✅ Favorites persist across sessions
- ✅ Recent list updates when items are dragged
- ✅ Sections collapse/expand smoothly
- ✅ Clear button resets search
- ✅ Tab switching is instant
- ✅ Empty states show helpful messages

---

### 1.2 Multi-Select & Bulk Operations

#### Implementation Details

**Current Limitation:**
```javascript
// Only single node selection
let selectedNode = null;
```

**New State:**
```javascript
// Multi-select support
let selectedNodes = new Set(); // Set of node IDs
let selectionMode = 'single'; // 'single' or 'multi'
let selectionBox = null; // For drag-select
```

**New HTML (Selection Controls):**
```html
<!-- Add to toolbar -->
<div class="toolbar-group">
    <button class="btn btn-secondary" onclick="selectAll()" title="Select all (Ctrl+A)">
        Select All
    </button>
    <button class="btn btn-secondary" onclick="deselectAll()" title="Deselect all (Esc)">
        Deselect All
    </button>
    <button class="btn btn-secondary" id="bulkActions" onclick="showBulkActionsMenu()"
            style="display: none;" title="Bulk actions for selected items">
        Actions (3 selected) ▼
    </button>
</div>

<!-- Bulk Actions Menu (Hidden) -->
<div id="bulkActionsMenu" class="context-menu" style="display: none;">
    <div class="context-menu-item" onclick="bulkAlign('left')">
        <span>⬅️ Align Left</span>
    </div>
    <div class="context-menu-item" onclick="bulkAlign('center')">
        <span>↔️ Align Center</span>
    </div>
    <div class="context-menu-item" onclick="bulkAlign('right')">
        <span>➡️ Align Right</span>
    </div>
    <div class="context-menu-divider"></div>
    <div class="context-menu-item" onclick="bulkDistribute('horizontal')">
        <span>↔️ Distribute Horizontally</span>
    </div>
    <div class="context-menu-item" onclick="bulkDistribute('vertical')">
        <span>↕️ Distribute Vertically</span>
    </div>
    <div class="context-menu-divider"></div>
    <div class="context-menu-item" onclick="bulkDuplicate()">
        <span>📋 Duplicate</span>
    </div>
    <div class="context-menu-item" onclick="bulkDelete()">
        <span style="color: #ef4444;">🗑️ Delete</span>
    </div>
</div>
```

**New CSS:**
```css
/* Multi-select styling */
.node.selected {
    border-color: var(--purple);
    box-shadow: 0 0 0 3px rgba(139, 92, 246, 0.3);
}

.node.multi-selected {
    border-color: var(--purple);
    box-shadow: 0 0 0 2px rgba(139, 92, 246, 0.2);
}

/* Selection box (drag-select) */
.selection-box {
    position: absolute;
    border: 2px dashed var(--purple);
    background: rgba(139, 92, 246, 0.1);
    pointer-events: none;
    z-index: 1000;
}

/* Selection count badge */
.selection-badge {
    position: fixed;
    bottom: 2rem;
    right: 2rem;
    background: var(--purple);
    color: white;
    padding: 0.75rem 1.5rem;
    border-radius: 2rem;
    font-weight: 600;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
    display: flex;
    align-items: center;
    gap: 0.5rem;
    animation: slideInUp 0.3s ease-out;
}

@keyframes slideInUp {
    from {
        transform: translateY(100%);
        opacity: 0;
    }
    to {
        transform: translateY(0);
        opacity: 1;
    }
}
```

**New JavaScript Functions:**
```javascript
// ============ Multi-Select System ============

function handleNodeClick(event, nodeId) {
    const node = canvasNodes.find(n => n.id === nodeId);
    if (!node) return;

    if (event.ctrlKey || event.metaKey) {
        // Ctrl+Click: Toggle selection
        if (selectedNodes.has(nodeId)) {
            selectedNodes.delete(nodeId);
        } else {
            selectedNodes.add(nodeId);
        }
    } else if (event.shiftKey) {
        // Shift+Click: Add to selection
        selectedNodes.add(nodeId);
    } else {
        // Regular click: Select only this node
        selectedNodes.clear();
        selectedNodes.add(nodeId);
    }

    updateSelectionUI();
    updatePropertiesPanel();
}

function updateSelectionUI() {
    // Update node styling
    document.querySelectorAll('.system-node, .integration-node').forEach(nodeEl => {
        const nodeId = nodeEl.getAttribute('data-node-id');

        if (selectedNodes.has(nodeId)) {
            nodeEl.classList.add(selectedNodes.size === 1 ? 'selected' : 'multi-selected');
        } else {
            nodeEl.classList.remove('selected', 'multi-selected');
        }
    });

    // Update toolbar
    const bulkActionsBtn = document.getElementById('bulkActions');
    if (selectedNodes.size > 1) {
        bulkActionsBtn.style.display = 'block';
        bulkActionsBtn.textContent = `Actions (${selectedNodes.size} selected) ▼`;
    } else {
        bulkActionsBtn.style.display = 'none';
    }

    // Show/hide selection badge
    updateSelectionBadge();
}

function updateSelectionBadge() {
    let badge = document.getElementById('selectionBadge');

    if (selectedNodes.size > 1) {
        if (!badge) {
            badge = document.createElement('div');
            badge.id = 'selectionBadge';
            badge.className = 'selection-badge';
            document.body.appendChild(badge);
        }

        badge.innerHTML = `
            <span>${selectedNodes.size} items selected</span>
            <button onclick="deselectAll()" style="background: rgba(255,255,255,0.2); border: none; border-radius: 50%; width: 1.5rem; height: 1.5rem; cursor: pointer; color: white;">✕</button>
        `;
    } else if (badge) {
        badge.remove();
    }
}

function updatePropertiesPanel() {
    const panel = document.getElementById('propertiesPanel');

    if (selectedNodes.size === 0) {
        // No selection
        panel.innerHTML = `
            <div class="empty-state">
                <div class="empty-state-icon">⚙️</div>
                <h3>Properties</h3>
                <p>Select a component to edit its properties</p>
            </div>
        `;
    } else if (selectedNodes.size === 1) {
        // Single selection - show full properties
        const nodeId = Array.from(selectedNodes)[0];
        const node = canvasNodes.find(n => n.id === nodeId);
        showProperties(node);
    } else {
        // Multi-selection - show bulk edit options
        panel.innerHTML = `
            <div class="properties-header">
                <h3>📦 Bulk Edit (${selectedNodes.size} items)</h3>
            </div>
            <div class="form-section">
                <p style="color: var(--text-secondary); font-size: 0.875rem; margin-bottom: 1rem;">
                    You have ${selectedNodes.size} items selected. Use the Actions button to perform bulk operations.
                </p>

                <h4 style="font-size: 0.875rem; margin: 1rem 0 0.5rem;">Quick Actions:</h4>
                <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 0.5rem;">
                    <button class="btn btn-secondary" onclick="bulkAlign('left')" style="font-size: 0.75rem;">⬅️ Align Left</button>
                    <button class="btn btn-secondary" onclick="bulkAlign('right')" style="font-size: 0.75rem;">➡️ Align Right</button>
                    <button class="btn btn-secondary" onclick="bulkDistribute('horizontal')" style="font-size: 0.75rem;">↔️ Distribute H</button>
                    <button class="btn btn-secondary" onclick="bulkDistribute('vertical')" style="font-size: 0.75rem;">↕️ Distribute V</button>
                </div>

                <h4 style="font-size: 0.875rem; margin: 1.5rem 0 0.5rem;">Bulk Settings:</h4>
                <div class="form-group">
                    <label>Environment</label>
                    <select onchange="bulkUpdateField('environment', this.value)">
                        <option value="">-- Keep Individual Values --</option>
                        <option value="development">Development</option>
                        <option value="test">Test</option>
                        <option value="staging">Staging</option>
                        <option value="production">Production</option>
                    </select>
                </div>

                <div class="form-group">
                    <label>Owner</label>
                    <input type="text" placeholder="Set owner for all selected" onchange="bulkUpdateField('owner', this.value)">
                </div>

                <button class="btn btn-danger" onclick="bulkDelete()" style="width: 100%; margin-top: 1rem;">
                    🗑️ Delete All Selected
                </button>
            </div>
        `;
    }
}

// ============ Drag-Select Box ============

let dragSelectStart = null;
let selectionBoxElement = null;

function initDragSelect() {
    const canvas = document.getElementById('canvas');

    canvas.addEventListener('mousedown', (e) => {
        // Only start drag-select if clicking on canvas background
        if (e.target === canvas || e.target.id === 'canvasInner') {
            // If not holding Ctrl/Shift, clear selection
            if (!e.ctrlKey && !e.metaKey && !e.shiftKey) {
                selectedNodes.clear();
            }

            dragSelectStart = {
                x: e.clientX,
                y: e.clientY
            };

            // Create selection box
            selectionBoxElement = document.createElement('div');
            selectionBoxElement.className = 'selection-box';
            selectionBoxElement.style.left = dragSelectStart.x + 'px';
            selectionBoxElement.style.top = dragSelectStart.y + 'px';
            document.body.appendChild(selectionBoxElement);
        }
    });

    document.addEventListener('mousemove', (e) => {
        if (dragSelectStart && selectionBoxElement) {
            const x = Math.min(e.clientX, dragSelectStart.x);
            const y = Math.min(e.clientY, dragSelectStart.y);
            const width = Math.abs(e.clientX - dragSelectStart.x);
            const height = Math.abs(e.clientY - dragSelectStart.y);

            selectionBoxElement.style.left = x + 'px';
            selectionBoxElement.style.top = y + 'px';
            selectionBoxElement.style.width = width + 'px';
            selectionBoxElement.style.height = height + 'px';

            // Highlight nodes within selection box
            highlightNodesInBox(x, y, width, height);
        }
    });

    document.addEventListener('mouseup', (e) => {
        if (dragSelectStart && selectionBoxElement) {
            // Finalize selection
            const x = Math.min(e.clientX, dragSelectStart.x);
            const y = Math.min(e.clientY, dragSelectStart.y);
            const width = Math.abs(e.clientX - dragSelectStart.x);
            const height = Math.abs(e.clientY - dragSelectStart.y);

            selectNodesInBox(x, y, width, height);

            // Clean up
            selectionBoxElement.remove();
            selectionBoxElement = null;
            dragSelectStart = null;

            updateSelectionUI();
        }
    });
}

function highlightNodesInBox(x, y, width, height) {
    document.querySelectorAll('.system-node, .integration-node').forEach(nodeEl => {
        const rect = nodeEl.getBoundingClientRect();

        // Check if node intersects with selection box
        const intersects = !(
            rect.right < x ||
            rect.left > x + width ||
            rect.bottom < y ||
            rect.top > y + height
        );

        if (intersects) {
            nodeEl.style.outline = '2px dashed var(--purple)';
        } else {
            nodeEl.style.outline = '';
        }
    });
}

function selectNodesInBox(x, y, width, height) {
    document.querySelectorAll('.system-node, .integration-node').forEach(nodeEl => {
        const rect = nodeEl.getBoundingClientRect();

        // Check if node intersects with selection box
        const intersects = !(
            rect.right < x ||
            rect.left > x + width ||
            rect.bottom < y ||
            rect.top > y + height
        );

        if (intersects) {
            const nodeId = nodeEl.getAttribute('data-node-id');
            selectedNodes.add(nodeId);
        }

        // Clear highlight
        nodeEl.style.outline = '';
    });
}

// ============ Bulk Operations ============

function selectAll() {
    selectedNodes.clear();
    canvasNodes.forEach(node => selectedNodes.add(node.id));
    updateSelectionUI();
    updatePropertiesPanel();
}

function deselectAll() {
    selectedNodes.clear();
    updateSelectionUI();
    updatePropertiesPanel();
}

function showBulkActionsMenu() {
    const menu = document.getElementById('bulkActionsMenu');
    const btn = document.getElementById('bulkActions');
    const rect = btn.getBoundingClientRect();

    menu.style.display = 'block';
    menu.style.left = rect.left + 'px';
    menu.style.top = (rect.bottom + 5) + 'px';

    // Close menu when clicking outside
    setTimeout(() => {
        document.addEventListener('click', function closeMenu(e) {
            if (!menu.contains(e.target) && e.target !== btn) {
                menu.style.display = 'none';
                document.removeEventListener('click', closeMenu);
            }
        });
    }, 0);
}

function bulkAlign(direction) {
    if (selectedNodes.size < 2) return;

    const nodes = Array.from(selectedNodes).map(id => canvasNodes.find(n => n.id === id));

    if (direction === 'left') {
        const minX = Math.min(...nodes.map(n => n.x));
        nodes.forEach(node => node.x = minX);
    } else if (direction === 'right') {
        const maxX = Math.max(...nodes.map(n => n.x + 200)); // 200 is node width
        nodes.forEach(node => node.x = maxX - 200);
    } else if (direction === 'center') {
        const minX = Math.min(...nodes.map(n => n.x));
        const maxX = Math.max(...nodes.map(n => n.x + 200));
        const centerX = (minX + maxX) / 2;
        nodes.forEach(node => node.x = centerX - 100); // 100 is half node width
    } else if (direction === 'top') {
        const minY = Math.min(...nodes.map(n => n.y));
        nodes.forEach(node => node.y = minY);
    } else if (direction === 'bottom') {
        const maxY = Math.max(...nodes.map(n => n.y + 150)); // 150 is node height
        nodes.forEach(node => node.y = maxY - 150);
    } else if (direction === 'middle') {
        const minY = Math.min(...nodes.map(n => n.y));
        const maxY = Math.max(...nodes.map(n => n.y + 150));
        const centerY = (minY + maxY) / 2;
        nodes.forEach(node => node.y = centerY - 75); // 75 is half node height
    }

    renderCanvas();
    saveHistory();
    showAlert('success', `Aligned ${nodes.length} items to ${direction}`);

    // Close menu
    document.getElementById('bulkActionsMenu').style.display = 'none';
}

function bulkDistribute(direction) {
    if (selectedNodes.size < 3) {
        showAlert('warning', 'Need at least 3 items to distribute');
        return;
    }

    const nodes = Array.from(selectedNodes)
        .map(id => canvasNodes.find(n => n.id === id))
        .sort((a, b) => direction === 'horizontal' ? a.x - b.x : a.y - b.y);

    if (direction === 'horizontal') {
        const minX = nodes[0].x;
        const maxX = nodes[nodes.length - 1].x;
        const spacing = (maxX - minX) / (nodes.length - 1);

        nodes.forEach((node, i) => {
            if (i > 0 && i < nodes.length - 1) {
                node.x = minX + spacing * i;
            }
        });
    } else {
        const minY = nodes[0].y;
        const maxY = nodes[nodes.length - 1].y;
        const spacing = (maxY - minY) / (nodes.length - 1);

        nodes.forEach((node, i) => {
            if (i > 0 && i < nodes.length - 1) {
                node.y = minY + spacing * i;
            }
        });
    }

    renderCanvas();
    saveHistory();
    showAlert('success', `Distributed ${nodes.length} items ${direction}ly`);

    // Close menu
    document.getElementById('bulkActionsMenu').style.display = 'none';
}

function bulkDuplicate() {
    if (selectedNodes.size === 0) return;

    const newNodes = [];

    selectedNodes.forEach(nodeId => {
        const node = canvasNodes.find(n => n.id === nodeId);
        if (!node) return;

        const newId = 'node-' + Date.now() + '-' + Math.random().toString(36).substr(2, 9);
        const newNode = {
            ...JSON.parse(JSON.stringify(node)),
            id: newId,
            x: node.x + 50,
            y: node.y + 50,
            data: {
                ...node.data,
                name: node.data.name + ' (Copy)'
            }
        };

        canvasNodes.push(newNode);
        newNodes.push(newId);

        // Update integration data
        if (node.type === 'system') {
            integrationData.systems[newId] = newNode.data;
        } else {
            integrationData.integrations.push({ ...newNode.data, id: newId });
        }
    });

    // Select the new nodes
    selectedNodes.clear();
    newNodes.forEach(id => selectedNodes.add(id));

    renderCanvas();
    updateSelectionUI();
    saveHistory();
    showAlert('success', `Duplicated ${newNodes.length} items`);

    // Close menu
    document.getElementById('bulkActionsMenu').style.display = 'none';
}

function bulkDelete() {
    if (selectedNodes.size === 0) return;

    const count = selectedNodes.size;

    if (!confirm(`Delete ${count} selected item${count > 1 ? 's' : ''}?`)) {
        return;
    }

    selectedNodes.forEach(nodeId => {
        const node = canvasNodes.find(n => n.id === nodeId);
        if (!node) return;

        if (node.type === 'system') {
            delete integrationData.systems[nodeId];
            // Remove references
            integrationData.integrations.forEach(i => {
                if (i.source.system === nodeId) i.source.system = '';
                if (i.target.system === nodeId) i.target.system = '';
            });
        } else {
            integrationData.integrations = integrationData.integrations.filter(i => i.id !== nodeId);
        }
    });

    // Remove from canvas
    canvasNodes = canvasNodes.filter(n => !selectedNodes.has(n.id));
    selectedNodes.clear();

    renderCanvas();
    updateSelectionUI();
    updatePropertiesPanel();
    saveHistory();
    showAlert('success', `Deleted ${count} items`);

    // Close menu
    document.getElementById('bulkActionsMenu').style.display = 'none';
}

function bulkUpdateField(field, value) {
    if (!value || selectedNodes.size === 0) return;

    selectedNodes.forEach(nodeId => {
        const node = canvasNodes.find(n => n.id === nodeId);
        if (!node) return;

        node.data[field] = value;

        if (node.type === 'system') {
            integrationData.systems[nodeId][field] = value;
        } else {
            const integration = integrationData.integrations.find(i => i.id === nodeId);
            if (integration) {
                if (field.includes('.')) {
                    // Handle nested fields (e.g., 'metadata.owner')
                    const parts = field.split('.');
                    let target = integration;
                    for (let i = 0; i < parts.length - 1; i++) {
                        if (!target[parts[i]]) target[parts[i]] = {};
                        target = target[parts[i]];
                    }
                    target[parts[parts.length - 1]] = value;
                } else {
                    integration[field] = value;
                }
            }
        }
    });

    saveHistory();
    showAlert('success', `Updated ${field} for ${selectedNodes.size} items`);
}

// Initialize on load
document.addEventListener('DOMContentLoaded', () => {
    initDragSelect();

    // Update existing node click handlers
    // This will be integrated into renderCanvas()
});
```

**Integration into Existing Code:**

Update `renderCanvas()` to add click handlers:
```javascript
function renderCanvas() {
    // ... existing code ...

    // Add click handler to each node
    nodeElement.addEventListener('click', (e) => {
        e.stopPropagation();
        handleNodeClick(e, node.id);
    });

    // ... rest of function ...
}
```

**Keyboard Shortcuts:**
```javascript
// Add to existing keyboard handler
document.addEventListener('keydown', (e) => {
    // ... existing shortcuts ...

    if (e.key === 'a' && (e.ctrlKey || e.metaKey)) {
        e.preventDefault();
        selectAll();
    }

    if (e.key === 'Escape') {
        deselectAll();
    }

    if (e.key === 'Delete' || e.key === 'Backspace') {
        if (selectedNodes.size > 0 && !e.target.matches('input, textarea')) {
            e.preventDefault();
            bulkDelete();
        }
    }

    // Alignment shortcuts
    if (e.ctrlKey || e.metaKey) {
        if (e.key === 'ArrowLeft') {
            e.preventDefault();
            bulkAlign('left');
        } else if (e.key === 'ArrowRight') {
            e.preventDefault();
            bulkAlign('right');
        } else if (e.key === 'ArrowUp') {
            e.preventDefault();
            bulkAlign('top');
        } else if (e.key === 'ArrowDown') {
            e.preventDefault();
            bulkAlign('bottom');
        }
    }
});
```

**Acceptance Criteria:**
- ✅ Ctrl+Click toggles individual selection
- ✅ Shift+Click adds to selection
- ✅ Drag on canvas creates selection box
- ✅ Selection box highlights nodes in real-time
- ✅ Bulk operations work on all selected nodes
- ✅ Alignment distributes evenly
- ✅ Properties panel shows bulk edit UI
- ✅ Keyboard shortcuts work (Ctrl+A, Esc, Delete)
- ✅ Performance: < 100ms for 50+ selected nodes

---

### 1.3 Grid & Alignment Guides

#### Implementation Details

**New HTML:**
```html
<!-- Add to toolbar -->
<div class="toolbar-group">
    <button class="btn btn-secondary btn-icon"
            onclick="toggleGrid()"
            id="gridToggle"
            title="Toggle grid (Ctrl+G)">
        #️⃣
    </button>
    <button class="btn btn-secondary btn-icon"
            onclick="toggleSnapToGrid()"
            id="snapToggle"
            title="Toggle snap to grid">
        🧲
    </button>
    <button class="btn btn-secondary btn-icon"
            onclick="toggleAlignmentGuides()"
            id="guidesToggle"
            title="Toggle alignment guides">
        📏
    </button>
</div>
```

**New CSS:**
```css
/* Grid Background */
.canvas.show-grid .canvas-inner {
    background-image:
        linear-gradient(to right, rgba(139, 92, 246, 0.1) 1px, transparent 1px),
        linear-gradient(to bottom, rgba(139, 92, 246, 0.1) 1px, transparent 1px);
    background-size: 20px 20px;
    background-position: 0 0;
}

/* Major grid lines every 100px */
.canvas.show-grid .canvas-inner::after {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-image:
        linear-gradient(to right, rgba(139, 92, 246, 0.2) 1px, transparent 1px),
        linear-gradient(to bottom, rgba(139, 92, 246, 0.2) 1px, transparent 1px);
    background-size: 100px 100px;
    background-position: 0 0;
    pointer-events: none;
}

/* Alignment Guides */
.alignment-guide {
    position: absolute;
    background: #f59e0b;
    pointer-events: none;
    z-index: 999;
    opacity: 0;
    transition: opacity 0.1s;
}

.alignment-guide.visible {
    opacity: 1;
}

.alignment-guide.vertical {
    width: 2px;
    height: 100%;
    top: 0;
}

.alignment-guide.horizontal {
    height: 2px;
    width: 100%;
    left: 0;
}

/* Active grid/snap indicators */
#gridToggle.active,
#snapToggle.active,
#guidesToggle.active {
    background: var(--purple);
    color: white;
}
```

**New JavaScript:**
```javascript
// ============ Grid & Alignment System ============

let gridSettings = {
    enabled: false,
    snapEnabled: false,
    guidesEnabled: true,
    gridSize: 20,
    majorGridSize: 100
};

// Load settings from localStorage
const savedGridSettings = localStorage.getItem('gridSettings');
if (savedGridSettings) {
    gridSettings = { ...gridSettings, ...JSON.parse(savedGridSettings) };
}

function toggleGrid() {
    gridSettings.enabled = !gridSettings.enabled;

    const canvas = document.getElementById('canvas');
    const btn = document.getElementById('gridToggle');

    if (gridSettings.enabled) {
        canvas.classList.add('show-grid');
        btn.classList.add('active');
    } else {
        canvas.classList.remove('show-grid');
        btn.classList.remove('active');
    }

    saveGridSettings();
}

function toggleSnapToGrid() {
    gridSettings.snapEnabled = !gridSettings.snapEnabled;

    const btn = document.getElementById('snapToggle');
    btn.classList.toggle('active', gridSettings.snapEnabled);

    if (gridSettings.snapEnabled) {
        showAlert('info', `Snap to grid enabled (${gridSettings.gridSize}px)`);
    }

    saveGridSettings();
}

function toggleAlignmentGuides() {
    gridSettings.guidesEnabled = !gridSettings.guidesEnabled;

    const btn = document.getElementById('guidesToggle');
    btn.classList.toggle('active', gridSettings.guidesEnabled);

    saveGridSettings();
}

function saveGridSettings() {
    localStorage.setItem('gridSettings', JSON.stringify(gridSettings));
}

function snapToGrid(value) {
    if (!gridSettings.snapEnabled) return value;
    return Math.round(value / gridSettings.gridSize) * gridSettings.gridSize;
}

// ============ Alignment Guides ============

let alignmentGuides = {
    vertical: [],
    horizontal: [],
    elements: []
};

function createAlignmentGuide(type, position) {
    const guide = document.createElement('div');
    guide.className = `alignment-guide ${type}`;

    if (type === 'vertical') {
        guide.style.left = position + 'px';
    } else {
        guide.style.top = position + 'px';
    }

    const canvas = document.getElementById('canvas');
    canvas.appendChild(guide);

    return guide;
}

function showAlignmentGuides(draggingNode) {
    if (!gridSettings.guidesEnabled) return;

    // Clear existing guides
    clearAlignmentGuides();

    const SNAP_DISTANCE = 10;
    const canvasRect = document.getElementById('canvas').getBoundingClientRect();

    // Get all other nodes
    const otherNodes = canvasNodes.filter(n => n.id !== draggingNode.id);

    // Check for vertical alignment (left, center, right edges)
    const draggingLeft = draggingNode.x;
    const draggingCenterX = draggingNode.x + 100; // Half of node width (200px)
    const draggingRight = draggingNode.x + 200;

    otherNodes.forEach(node => {
        const nodeLeft = node.x;
        const nodeCenterX = node.x + 100;
        const nodeRight = node.x + 200;

        // Check left edge alignment
        if (Math.abs(draggingLeft - nodeLeft) < SNAP_DISTANCE) {
            const guide = createAlignmentGuide('vertical', (nodeLeft * zoomLevel) + canvasOffset.x);
            guide.classList.add('visible');
            alignmentGuides.elements.push(guide);
            alignmentGuides.vertical.push(nodeLeft);
        }

        // Check center alignment
        if (Math.abs(draggingCenterX - nodeCenterX) < SNAP_DISTANCE) {
            const guide = createAlignmentGuide('vertical', (nodeCenterX * zoomLevel) + canvasOffset.x);
            guide.classList.add('visible');
            alignmentGuides.elements.push(guide);
            alignmentGuides.vertical.push(nodeCenterX);
        }

        // Check right edge alignment
        if (Math.abs(draggingRight - nodeRight) < SNAP_DISTANCE) {
            const guide = createAlignmentGuide('vertical', (nodeRight * zoomLevel) + canvasOffset.x);
            guide.classList.add('visible');
            alignmentGuides.elements.push(guide);
            alignmentGuides.vertical.push(nodeRight);
        }
    });

    // Check for horizontal alignment (top, middle, bottom edges)
    const draggingTop = draggingNode.y;
    const draggingCenterY = draggingNode.y + 75; // Half of node height (150px)
    const draggingBottom = draggingNode.y + 150;

    otherNodes.forEach(node => {
        const nodeTop = node.y;
        const nodeCenterY = node.y + 75;
        const nodeBottom = node.y + 150;

        // Check top edge alignment
        if (Math.abs(draggingTop - nodeTop) < SNAP_DISTANCE) {
            const guide = createAlignmentGuide('horizontal', (nodeTop * zoomLevel) + canvasOffset.y);
            guide.classList.add('visible');
            alignmentGuides.elements.push(guide);
            alignmentGuides.horizontal.push(nodeTop);
        }

        // Check middle alignment
        if (Math.abs(draggingCenterY - nodeCenterY) < SNAP_DISTANCE) {
            const guide = createAlignmentGuide('horizontal', (nodeCenterY * zoomLevel) + canvasOffset.y);
            guide.classList.add('visible');
            alignmentGuides.elements.push(guide);
            alignmentGuides.horizontal.push(nodeCenterY);
        }

        // Check bottom edge alignment
        if (Math.abs(draggingBottom - nodeBottom) < SNAP_DISTANCE) {
            const guide = createAlignmentGuide('horizontal', (nodeBottom * zoomLevel) + canvasOffset.y);
            guide.classList.add('visible');
            alignmentGuides.elements.push(guide);
            alignmentGuides.horizontal.push(nodeBottom);
        }
    });
}

function clearAlignmentGuides() {
    alignmentGuides.elements.forEach(el => el.remove());
    alignmentGuides.elements = [];
    alignmentGuides.vertical = [];
    alignmentGuides.horizontal = [];
}

function snapToAlignment(value, guides) {
    if (!gridSettings.guidesEnabled) return value;

    const SNAP_DISTANCE = 10;

    for (const guidePos of guides) {
        if (Math.abs(value - guidePos) < SNAP_DISTANCE) {
            return guidePos;
        }
    }

    return value;
}

// ============ Integration with Drag System ============

// Update existing node drag handler
function onNodeDragMove(e, node) {
    if (!isDraggingNode || !currentDragNode) return;

    const canvas = document.getElementById('canvas');
    const rect = canvas.getBoundingClientRect();
    const { clientX, clientY } = getEventCoords(e);

    let newX = (clientX - rect.left - currentDragNode.offsetX - canvasOffset.x) / zoomLevel;
    let newY = (clientY - rect.top - currentDragNode.offsetY - canvasOffset.y) / zoomLevel;

    // Apply snap to grid
    if (gridSettings.snapEnabled) {
        newX = snapToGrid(newX);
        newY = snapToGrid(newY);
    }

    // Apply snap to alignment guides
    if (gridSettings.guidesEnabled) {
        newX = snapToAlignment(newX, alignmentGuides.vertical);
        newY = snapToAlignment(newY, alignmentGuides.horizontal);
    }

    node.x = Math.max(0, newX);
    node.y = Math.max(0, newY);

    // Update position
    const nodeElement = document.querySelector(`[data-node-id="${node.id}"]`);
    if (nodeElement) {
        nodeElement.style.left = node.x + 'px';
        nodeElement.style.top = node.y + 'px';
    }

    // Show alignment guides
    showAlignmentGuides(node);

    // Update connections
    updateNodeConnections(node.id);
}

function onNodeDragEnd(e, node) {
    if (!isDraggingNode) return;

    isDraggingNode = false;
    currentDragNode = null;

    // Clear alignment guides
    clearAlignmentGuides();

    // Save to history
    saveHistory();
}

// Initialize grid settings on load
document.addEventListener('DOMContentLoaded', () => {
    // Apply saved settings
    if (gridSettings.enabled) {
        document.getElementById('canvas').classList.add('show-grid');
        document.getElementById('gridToggle').classList.add('active');
    }
    if (gridSettings.snapEnabled) {
        document.getElementById('snapToggle').classList.add('active');
    }
    if (gridSettings.guidesEnabled) {
        document.getElementById('guidesToggle').classList.add('active');
    }
});

// Add keyboard shortcut
document.addEventListener('keydown', (e) => {
    if (e.key === 'g' && (e.ctrlKey || e.metaKey)) {
        e.preventDefault();
        toggleGrid();
    }
});
```

**Acceptance Criteria:**
- ✅ Grid displays at 20px intervals with 100px major lines
- ✅ Snap to grid rounds to nearest grid point
- ✅ Alignment guides appear when within 10px of another node edge
- ✅ Guides show for left/center/right and top/middle/bottom alignment
- ✅ Snapping feels natural (not jarring)
- ✅ Settings persist across sessions
- ✅ Keyboard shortcut (Ctrl+G) toggles grid
- ✅ Visual feedback (button highlights when active)

---

### 1.4 Mini-Map Navigation

#### Implementation Details

**New HTML:**
```html
<!-- Mini-map in bottom-right corner -->
<div class="mini-map" id="miniMap">
    <div class="mini-map-header">
        <span>Map</span>
        <button onclick="toggleMiniMap()" class="btn btn-icon" title="Toggle mini-map">⊙</button>
        <button onclick="closeMiniMap()" class="btn btn-icon" title="Close">✕</button>
    </div>
    <div class="mini-map-canvas" id="miniMapCanvas">
        <!-- Rendered programmatically -->
    </div>
    <div class="mini-map-viewport" id="miniMapViewport"></div>
</div>
```

**New CSS:**
```css
/* Mini-Map */
.mini-map {
    position: fixed;
    bottom: 1rem;
    right: 1rem;
    width: 200px;
    height: 150px;
    background: white;
    border: 2px solid var(--border);
    border-radius: 0.5rem;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
    z-index: 100;
    display: flex;
    flex-direction: column;
    transition: all 0.3s;
}

.mini-map.minimized {
    width: 40px;
    height: 40px;
    overflow: hidden;
}

.mini-map.minimized .mini-map-canvas,
.mini-map.minimized .mini-map-viewport {
    display: none;
}

.mini-map.hidden {
    display: none;
}

.mini-map-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 0.25rem 0.5rem;
    background: var(--bg-secondary);
    border-bottom: 1px solid var(--border);
    font-size: 0.75rem;
    font-weight: 600;
    color: var(--text-secondary);
}

.mini-map-header .btn-icon {
    padding: 0.125rem 0.25rem;
    font-size: 0.75rem;
    background: none;
    border: none;
    cursor: pointer;
    color: var(--text-secondary);
}

.mini-map-header .btn-icon:hover {
    color: var(--text-primary);
}

.mini-map-canvas {
    flex: 1;
    position: relative;
    overflow: hidden;
    background: var(--bg-secondary);
}

.mini-map-node {
    position: absolute;
    border-radius: 2px;
    border: 1px solid;
}

.mini-map-node.system {
    background: rgba(59, 130, 246, 0.7);
    border-color: #3b82f6;
}

.mini-map-node.integration {
    background: rgba(139, 92, 246, 0.7);
    border-color: #8b5cf6;
}

.mini-map-connection {
    position: absolute;
    height: 1px;
    background: rgba(139, 92, 246, 0.3);
    transform-origin: left center;
}

.mini-map-viewport {
    position: absolute;
    border: 2px solid var(--purple);
    background: rgba(139, 92, 246, 0.1);
    cursor: move;
    pointer-events: all;
}

/* Resize handle */
.mini-map::before {
    content: '↘';
    position: absolute;
    bottom: 0.25rem;
    right: 0.25rem;
    font-size: 0.75rem;
    color: var(--text-secondary);
    cursor: nwse-resize;
    pointer-events: none;
}
```

**New JavaScript:**
```javascript
// ============ Mini-Map System ============

let miniMapState = {
    enabled: true,
    minimized: false,
    scale: 1,
    updateInterval: null
};

function renderMiniMap() {
    if (!miniMapState.enabled || miniMapState.minimized) return;

    const container = document.getElementById('miniMapCanvas');
    if (!container) return;

    // Clear existing content
    container.innerHTML = '';

    if (canvasNodes.length === 0) {
        container.innerHTML = '<p style="text-align: center; color: var(--text-secondary); font-size: 0.625rem; padding: 1rem;">No nodes</p>';
        return;
    }

    // Calculate bounds
    let minX = Infinity, minY = Infinity, maxX = -Infinity, maxY = -Infinity;

    canvasNodes.forEach(node => {
        minX = Math.min(minX, node.x);
        minY = Math.min(minY, node.y);
        maxX = Math.max(maxX, node.x + 200);
        maxY = Math.max(maxY, node.y + 150);
    });

    const contentWidth = maxX - minX;
    const contentHeight = maxY - minY;

    // Calculate scale to fit in mini-map
    const mapWidth = container.clientWidth;
    const mapHeight = container.clientHeight;

    miniMapState.scale = Math.min(mapWidth / contentWidth, mapHeight / contentHeight, 1);

    // Render nodes
    canvasNodes.forEach(node => {
        const nodeEl = document.createElement('div');
        nodeEl.className = `mini-map-node ${node.type}`;

        const x = (node.x - minX) * miniMapState.scale;
        const y = (node.y - minY) * miniMapState.scale;
        const width = 200 * miniMapState.scale;
        const height = 150 * miniMapState.scale;

        nodeEl.style.left = x + 'px';
        nodeEl.style.top = y + 'px';
        nodeEl.style.width = width + 'px';
        nodeEl.style.height = height + 'px';

        nodeEl.setAttribute('data-node-id', node.id);

        // Click to select node
        nodeEl.addEventListener('click', () => {
            selectedNodes.clear();
            selectedNodes.add(node.id);
            updateSelectionUI();
            centerOnNode(node.id);
        });

        container.appendChild(nodeEl);
    });

    // Render connections (simplified)
    integrationData.integrations.forEach(integration => {
        if (!integration.source.system || !integration.target.system) return;

        const sourceNode = canvasNodes.find(n => n.id === integration.source.system);
        const targetNode = canvasNodes.find(n => n.id === integration.target.system);

        if (!sourceNode || !targetNode) return;

        const x1 = (sourceNode.x + 100 - minX) * miniMapState.scale;
        const y1 = (sourceNode.y + 75 - minY) * miniMapState.scale;
        const x2 = (targetNode.x + 100 - minX) * miniMapState.scale;
        const y2 = (targetNode.y + 75 - minY) * miniMapState.scale;

        const line = document.createElement('div');
        line.className = 'mini-map-connection';

        const length = Math.sqrt((x2 - x1) ** 2 + (y2 - y1) ** 2);
        const angle = Math.atan2(y2 - y1, x2 - x1) * 180 / Math.PI;

        line.style.left = x1 + 'px';
        line.style.top = y1 + 'px';
        line.style.width = length + 'px';
        line.style.transform = `rotate(${angle}deg)`;

        container.appendChild(line);
    });

    // Update viewport rectangle
    updateMiniMapViewport();
}

function updateMiniMapViewport() {
    if (!miniMapState.enabled || miniMapState.minimized) return;

    const viewport = document.getElementById('miniMapViewport');
    const container = document.getElementById('miniMapCanvas');

    if (!viewport || !container) return;

    // Calculate viewport position and size
    const canvas = document.getElementById('canvas');
    const canvasRect = canvas.getBoundingClientRect();

    // Calculate what's visible in the main canvas
    const visibleLeft = -canvasOffset.x / zoomLevel;
    const visibleTop = -canvasOffset.y / zoomLevel;
    const visibleWidth = canvasRect.width / zoomLevel;
    const visibleHeight = canvasRect.height / zoomLevel;

    // Calculate bounds
    let minX = Infinity, minY = Infinity;
    canvasNodes.forEach(node => {
        minX = Math.min(minX, node.x);
        minY = Math.min(minY, node.y);
    });

    // Position viewport rectangle
    const x = (visibleLeft - minX) * miniMapState.scale;
    const y = (visibleTop - minY) * miniMapState.scale;
    const width = visibleWidth * miniMapState.scale;
    const height = visibleHeight * miniMapState.scale;

    viewport.style.left = Math.max(0, x) + 'px';
    viewport.style.top = Math.max(0, y) + 'px';
    viewport.style.width = Math.min(width, container.clientWidth) + 'px';
    viewport.style.height = Math.min(height, container.clientHeight) + 'px';
}

function toggleMiniMap() {
    miniMapState.minimized = !miniMapState.minimized;

    const miniMap = document.getElementById('miniMap');
    miniMap.classList.toggle('minimized', miniMapState.minimized);

    if (!miniMapState.minimized) {
        renderMiniMap();
    }
}

function closeMiniMap() {
    miniMapState.enabled = false;
    document.getElementById('miniMap').classList.add('hidden');
    localStorage.setItem('miniMapEnabled', 'false');
}

function openMiniMap() {
    miniMapState.enabled = true;
    miniMapState.minimized = false;
    document.getElementById('miniMap').classList.remove('hidden', 'minimized');
    renderMiniMap();
    localStorage.setItem('miniMapEnabled', 'true');
}

function centerOnNode(nodeId) {
    const node = canvasNodes.find(n => n.id === nodeId);
    if (!node) return;

    const canvas = document.getElementById('canvas');
    const rect = canvas.getBoundingClientRect();

    // Center the node in the viewport
    canvasOffset.x = (rect.width / 2) - (node.x * zoomLevel) - (100 * zoomLevel);
    canvasOffset.y = (rect.height / 2) - (node.y * zoomLevel) - (75 * zoomLevel);

    updateCanvasTransform();
    updateMiniMapViewport();
}

// Initialize mini-map viewport dragging
function initMiniMapViewportDrag() {
    const viewport = document.getElementById('miniMapViewport');
    let isDragging = false;
    let startX, startY;

    viewport.addEventListener('mousedown', (e) => {
        isDragging = true;
        startX = e.clientX;
        startY = e.clientY;
        e.stopPropagation();
    });

    document.addEventListener('mousemove', (e) => {
        if (!isDragging) return;

        const dx = (e.clientX - startX) / miniMapState.scale;
        const dy = (e.clientY - startY) / miniMapState.scale;

        canvasOffset.x -= dx * zoomLevel;
        canvasOffset.y -= dy * zoomLevel;

        updateCanvasTransform();
        updateMiniMapViewport();

        startX = e.clientX;
        startY = e.clientY;
    });

    document.addEventListener('mouseup', () => {
        isDragging = false;
    });
}

// Update mini-map periodically
function startMiniMapUpdates() {
    miniMapState.updateInterval = setInterval(() => {
        if (miniMapState.enabled && !miniMapState.minimized) {
            renderMiniMap();
        }
    }, 2000); // Update every 2 seconds
}

function stopMiniMapUpdates() {
    if (miniMapState.updateInterval) {
        clearInterval(miniMapState.updateInterval);
        miniMapState.updateInterval = null;
    }
}

// Initialize on load
document.addEventListener('DOMContentLoaded', () => {
    const enabled = localStorage.getItem('miniMapEnabled');
    if (enabled === 'false') {
        closeMiniMap();
    } else {
        openMiniMap();
    }

    initMiniMapViewportDrag();
    startMiniMapUpdates();
});

// Update mini-map when canvas changes
const originalRenderCanvas = renderCanvas;
renderCanvas = function() {
    originalRenderCanvas();
    if (miniMapState.enabled && !miniMapState.minimized) {
        setTimeout(renderMiniMap, 100); // Debounce
    }
};

// Update mini-map viewport when panning/zooming
const originalUpdateCanvasTransform = updateCanvasTransform;
updateCanvasTransform = function() {
    originalUpdateCanvasTransform();
    updateMiniMapViewport();
};
```

**Acceptance Criteria:**
- ✅ Mini-map shows all nodes and connections
- ✅ Different colors for systems vs integrations
- ✅ Viewport rectangle shows current view
- ✅ Clicking node in mini-map centers it in main canvas
- ✅ Dragging viewport rectangle pans main canvas
- ✅ Can minimize/restore/close mini-map
- ✅ Updates automatically when nodes change
- ✅ Performance: < 50ms to render 100+ nodes
- ✅ Settings persist across sessions

---

### 1.5 Enhanced Keyboard Shortcuts

#### Implementation Details

**New HTML (Help Overlay):**
```html
<!-- Keyboard Shortcuts Help -->
<div class="modal" id="shortcutsModal">
    <div class="modal-content" style="max-width: 800px;">
        <div class="modal-header">
            <h2>⌨️ Keyboard Shortcuts</h2>
            <button class="close-btn" onclick="closeShortcutsModal()">✕</button>
        </div>

        <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 2rem;">
            <div>
                <h3 style="font-size: 1rem; margin-bottom: 1rem; color: var(--purple);">General</h3>
                <table class="shortcuts-table">
                    <tr>
                        <td><kbd>?</kbd></td>
                        <td>Show shortcuts</td>
                    </tr>
                    <tr>
                        <td><kbd>Ctrl</kbd> + <kbd>Z</kbd></td>
                        <td>Undo</td>
                    </tr>
                    <tr>
                        <td><kbd>Ctrl</kbd> + <kbd>Y</kbd></td>
                        <td>Redo</td>
                    </tr>
                    <tr>
                        <td><kbd>Ctrl</kbd> + <kbd>S</kbd></td>
                        <td>Save (Export JSON)</td>
                    </tr>
                    <tr>
                        <td><kbd>Ctrl</kbd> + <kbd>O</kbd></td>
                        <td>Open (Import JSON)</td>
                    </tr>
                    <tr>
                        <td><kbd>Esc</kbd></td>
                        <td>Deselect all / Cancel</td>
                    </tr>
                </table>

                <h3 style="font-size: 1rem; margin: 1.5rem 0 1rem; color: var(--purple);">Selection</h3>
                <table class="shortcuts-table">
                    <tr>
                        <td><kbd>Ctrl</kbd> + <kbd>A</kbd></td>
                        <td>Select all</td>
                    </tr>
                    <tr>
                        <td><kbd>Ctrl</kbd> + <kbd>Click</kbd></td>
                        <td>Toggle selection</td>
                    </tr>
                    <tr>
                        <td><kbd>Shift</kbd> + <kbd>Click</kbd></td>
                        <td>Add to selection</td>
                    </tr>
                    <tr>
                        <td><kbd>Delete</kbd> / <kbd>Backspace</kbd></td>
                        <td>Delete selected</td>
                    </tr>
                    <tr>
                        <td><kbd>Ctrl</kbd> + <kbd>D</kbd></td>
                        <td>Duplicate selected</td>
                    </tr>
                </table>
            </div>

            <div>
                <h3 style="font-size: 1rem; margin-bottom: 1rem; color: var(--purple);">Canvas</h3>
                <table class="shortcuts-table">
                    <tr>
                        <td><kbd>Space</kbd> + <kbd>Drag</kbd></td>
                        <td>Pan canvas</td>
                    </tr>
                    <tr>
                        <td><kbd>Ctrl</kbd> + <kbd>+</kbd></td>
                        <td>Zoom in</td>
                    </tr>
                    <tr>
                        <td><kbd>Ctrl</kbd> + <kbd>-</kbd></td>
                        <td>Zoom out</td>
                    </tr>
                    <tr>
                        <td><kbd>Ctrl</kbd> + <kbd>0</kbd></td>
                        <td>Reset zoom</td>
                    </tr>
                    <tr>
                        <td><kbd>Ctrl</kbd> + <kbd>F</kbd></td>
                        <td>Fit to view</td>
                    </tr>
                    <tr>
                        <td><kbd>Ctrl</kbd> + <kbd>G</kbd></td>
                        <td>Toggle grid</td>
                    </tr>
                </table>

                <h3 style="font-size: 1rem; margin: 1.5rem 0 1rem; color: var(--purple);">Alignment</h3>
                <table class="shortcuts-table">
                    <tr>
                        <td><kbd>Ctrl</kbd> + <kbd>←</kbd></td>
                        <td>Align left</td>
                    </tr>
                    <tr>
                        <td><kbd>Ctrl</kbd> + <kbd>→</kbd></td>
                        <td>Align right</td>
                    </tr>
                    <tr>
                        <td><kbd>Ctrl</kbd> + <kbd>↑</kbd></td>
                        <td>Align top</td>
                    </tr>
                    <tr>
                        <td><kbd>Ctrl</kbd> + <kbd>↓</kbd></td>
                        <td>Align bottom</td>
                    </tr>
                </table>

                <h3 style="font-size: 1rem; margin: 1.5rem 0 1rem; color: var(--purple);">Search</h3>
                <table class="shortcuts-table">
                    <tr>
                        <td><kbd>Ctrl</kbd> + <kbd>K</kbd></td>
                        <td>Focus search</td>
                    </tr>
                </table>
            </div>
        </div>

        <div style="margin-top: 1.5rem; padding-top: 1rem; border-top: 1px solid var(--border); text-align: center;">
            <button class="btn btn-primary" onclick="closeShortcutsModal()">Got it!</button>
        </div>
    </div>
</div>
```

**New CSS:**
```css
.shortcuts-table {
    width: 100%;
    font-size: 0.875rem;
}

.shortcuts-table td {
    padding: 0.5rem 0;
    border-bottom: 1px solid var(--border);
}

.shortcuts-table td:first-child {
    font-weight: 600;
    color: var(--text-secondary);
    width: 140px;
}

.shortcuts-table tr:last-child td {
    border-bottom: none;
}

kbd {
    display: inline-block;
    padding: 0.125rem 0.375rem;
    background: var(--bg-secondary);
    border: 1px solid var(--border);
    border-radius: 0.25rem;
    font-family: monospace;
    font-size: 0.75rem;
    font-weight: 600;
    box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05);
}
```

**Enhanced JavaScript:**
```javascript
// ============ Comprehensive Keyboard Shortcuts ============

document.addEventListener('keydown', (e) => {
    // Ignore if typing in input
    if (e.target.matches('input, textarea, select')) {
        return;
    }

    const ctrl = e.ctrlKey || e.metaKey;
    const shift = e.shiftKey;
    const alt = e.altKey;

    // Show shortcuts help
    if (e.key === '?' && !ctrl && !shift) {
        e.preventDefault();
        showShortcutsModal();
        return;
    }

    // Undo/Redo
    if (ctrl && e.key === 'z' && !shift) {
        e.preventDefault();
        undo();
        return;
    }
    if (ctrl && (e.key === 'y' || (e.key === 'z' && shift))) {
        e.preventDefault();
        redo();
        return;
    }

    // Save/Open
    if (ctrl && e.key === 's') {
        e.preventDefault();
        exportJSON();
        return;
    }
    if (ctrl && e.key === 'o') {
        e.preventDefault();
        document.getElementById('jsonFileInput').click();
        return;
    }

    // Selection
    if (ctrl && e.key === 'a') {
        e.preventDefault();
        selectAll();
        return;
    }
    if (e.key === 'Escape') {
        deselectAll();
        return;
    }
    if (ctrl && e.key === 'd') {
        e.preventDefault();
        if (selectedNodes.size > 0) {
            bulkDuplicate();
        }
        return;
    }

    // Delete
    if (e.key === 'Delete' || e.key === 'Backspace') {
        if (selectedNodes.size > 0) {
            e.preventDefault();
            bulkDelete();
        }
        return;
    }

    // Canvas navigation
    if (ctrl && e.key === '=') {
        e.preventDefault();
        zoomIn();
        return;
    }
    if (ctrl && e.key === '-') {
        e.preventDefault();
        zoomOut();
        return;
    }
    if (ctrl && e.key === '0') {
        e.preventDefault();
        resetZoom();
        return;
    }
    if (ctrl && e.key === 'f') {
        e.preventDefault();
        fitToView();
        return;
    }
    if (ctrl && e.key === 'g') {
        e.preventDefault();
        toggleGrid();
        return;
    }

    // Alignment (with Ctrl)
    if (ctrl && !shift && !alt) {
        if (e.key === 'ArrowLeft') {
            e.preventDefault();
            bulkAlign('left');
            return;
        }
        if (e.key === 'ArrowRight') {
            e.preventDefault();
            bulkAlign('right');
            return;
        }
        if (e.key === 'ArrowUp') {
            e.preventDefault();
            bulkAlign('top');
            return;
        }
        if (e.key === 'ArrowDown') {
            e.preventDefault();
            bulkAlign('bottom');
            return;
        }
    }

    // Search focus
    if (ctrl && e.key === 'k') {
        e.preventDefault();
        document.getElementById('paletteSearch')?.focus();
        return;
    }

    // Space for panning
    if (e.key === ' ' && !e.repeat) {
        e.preventDefault();
        document.getElementById('canvas').style.cursor = 'grab';
        return;
    }
});

document.addEventListener('keyup', (e) => {
    if (e.key === ' ') {
        document.getElementById('canvas').style.cursor = '';
    }
});

function showShortcutsModal() {
    const modal = document.getElementById('shortcutsModal');
    if (!modal) return;

    modal.classList.add('active');
    modal.style.display = 'flex';
}

function closeShortcutsModal() {
    const modal = document.getElementById('shortcutsModal');
    if (!modal) return;

    modal.classList.remove('active');
    modal.style.display = 'none';
}

// Show shortcuts hint on first load
document.addEventListener('DOMContentLoaded', () => {
    const hasSeenShortcuts = localStorage.getItem('hasSeenShortcuts');

    if (!hasSeenShortcuts) {
        setTimeout(() => {
            showAlert('info', 'Press ? to see keyboard shortcuts', 5000);
            localStorage.setItem('hasSeenShortcuts', 'true');
        }, 2000);
    }
});
```

**Acceptance Criteria:**
- ✅ All shortcuts work as documented
- ✅ Shortcuts don't interfere with input fields
- ✅ ? shows comprehensive help modal
- ✅ Keyboard-only workflow is fully functional
- ✅ Visual feedback for shortcut actions
- ✅ Mac (Cmd) and Windows (Ctrl) support
- ✅ First-time hint appears

---

## Phase 1 Summary

**Completed Features:**
1. ✅ Searchable & categorized palette with favorites
2. ✅ Multi-select and bulk operations
3. ✅ Grid snapping and alignment guides
4. ✅ Mini-map navigation
5. ✅ Enhanced keyboard shortcuts

**Testing Checklist:**
- [ ] All features work in Chrome, Firefox, Safari
- [ ] Touch support works on tablets
- [ ] Performance test with 100+ nodes
- [ ] All keyboard shortcuts functional
- [ ] Settings persist across sessions
- [ ] No console errors

**Duration:** 2 weeks
**Lines of Code:** ~2,500 new
**Files Modified:** visual-designer.html

---

*To be continued in Phase 2...*

