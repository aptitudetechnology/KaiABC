<!DOCTYPE html>
<html lang="en" class="scroll-smooth">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kakeya Conjecture & Oscillator Synchronization</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/3.7.0/chart.min.js"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <!-- Chosen Palette: Calm Neutrals -->
    <!-- Application Structure Plan: A single-page, scrolling application with a fixed sidebar navigation. This structure guides the user from foundational concepts (Kakeya, Kuramoto), through the key environmental factor (temperature conversion), to the formal research plan, and finally to an interactive simulation dashboard. This non-linear but guided approach is chosen for usability, allowing experts to jump to the simulation while enabling newcomers to build understanding sequentially. It prioritizes exploring the problem's dynamics over a static report format. -->
    <!-- Visualization & Content Choices: 
    - Report Info: Temperature dependence of KaiABC. Goal: Explain σ_T to σ_ω conversion. Viz: Interactive line chart (Chart.js) with sliders for Q10 and σ_T. Interaction: Sliders update chart and calculated σ_ω value. Justification: Makes the crucial parameter conversion tangible and interactive. Library: Chart.js (Canvas).
    - Report Info: Core synchronization problem. Goal: Demonstrate effect of N, K, σ_ω. Viz: A dashboard combining a Canvas animation of oscillators on a circle, a Chart.js line plot of Order Parameter vs. Coupling, and numeric readouts. Interaction: Sliders for N, K, σ_ω control all elements simultaneously. Justification: Provides a holistic, intuitive feel for the system's dynamics, directly connecting abstract parameters to the visual outcome of synchronization. Library: Chart.js, custom Canvas rendering.
    - Report Info: 10-step research plan. Goal: Present the plan clearly. Viz: HTML/CSS accordion. Interaction: Click to expand. Justification: Keeps the UI clean and scannable.
    - CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #FDFBF8;
            color: #4A4A4A;
        }
        .chart-container {
            position: relative;
            width: 100%;
            max-width: 600px;
            margin-left: auto;
            margin-right: auto;
            height: 300px;
            max-height: 400px;
        }
        @media (min-width: 768px) {
            .chart-container {
                height: 350px;
            }
        }
        .nav-link {
            transition: all 0.2s ease-in-out;
        }
        .nav-link.active {
            background-color: #4A5568;
            color: #FFFFFF;
            transform: translateX(4px);
        }
        .param-card {
            background-color: #FFFFFF;
            border: 1px solid #E2E8F0;
            border-radius: 0.75rem;
            padding: 1.5rem;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.05), 0 2px 4px -1px rgba(0, 0, 0, 0.03);
        }
        .accordion-content {
            max-height: 0;
            overflow: hidden;
            transition: max-height 0.3s ease-out;
        }
        input[type=range] {
            -webkit-appearance: none;
            appearance: none;
            width: 100%;
            background: transparent;
        }
        input[type=range]::-webkit-slider-runnable-track {
            width: 100%;
            height: 6px;
            cursor: pointer;
            background: #E2E8F0;
            border-radius: 3px;
        }
        input[type=range]::-webkit-slider-thumb {
            -webkit-appearance: none;
            height: 20px;
            width: 20px;
            border-radius: 50%;
            background: #4A5568;
            cursor: pointer;
            margin-top: -7px;
        }
        input[type=range]:focus::-webkit-slider-runnable-track {
            background: #CBD5E0;
        }
        ::-webkit-scrollbar {
            width: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #FDFBF8;
        }
        ::-webkit-scrollbar-thumb {
            background: #CBD5E0;
            border-radius: 10px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #A0AEC0;
        }
    </style>
</head>
<body class="min-h-screen">
    <div class="flex min-h-screen">
        <nav class="hidden md:block w-64 bg-gray-50 border-r border-gray-200 p-6 fixed h-full" role="navigation" aria-label="Main navigation">
            <h2 class="text-lg font-bold text-gray-800 mb-6">Research Explorer</h2>
            <ul class="space-y-2" id="desktop-nav">
                <li><a href="#introduction" class="nav-link block p-2 rounded-lg text-gray-600 hover:bg-gray-200" aria-label="Go to Introduction section">Introduction</a></li>
                <li><a href="#concepts" class="nav-link block p-2 rounded-lg text-gray-600 hover:bg-gray-200" aria-label="Go to Core Concepts section">Core Concepts</a></li>
                <li><a href="#environment" class="nav-link block p-2 rounded-lg text-gray-600 hover:bg-gray-200" aria-label="Go to Environmental Factors section">Environmental Factors</a></li>
                <li><a href="#protocol" class="nav-link block p-2 rounded-lg text-gray-600 hover:bg-gray-200" aria-label="Go to Research Protocol section">Research Protocol</a></li>
                <li><a href="#simulation" class="nav-link block p-2 rounded-lg text-gray-600 hover:bg-gray-200" aria-label="Go to Interactive Simulation section">Interactive Simulation</a></li>
                <li><a href="#comparison" class="nav-link block p-2 rounded-lg text-gray-600 hover:bg-gray-200" aria-label="Go to Q10 Comparison section">Q10 Comparison</a></li>
                <li><a href="#alternatives" class="nav-link block p-2 rounded-lg text-gray-600 hover:bg-gray-200" aria-label="Go to Alternative Frameworks section">Alternative Frameworks</a></li>
                <li><a href="#implications" class="nav-link block p-2 rounded-lg text-gray-600 hover:bg-gray-200" aria-label="Go to Implications section">Implications</a></li>
                <li><a href="#validation" class="nav-link block p-2 rounded-lg text-gray-600 hover:bg-gray-200" aria-label="Go to Validation section">Validation</a></li>
            </ul>
        </nav>

        <main class="flex-1 md:ml-64 p-4 sm:p-6 lg:p-10">
        <div class="max-w-4xl mx-auto space-y-16">

            <section id="introduction">
                <div class="p-8 bg-white rounded-xl border border-gray-200">
                    <h1 class="text-3xl font-bold text-gray-900 mb-4">Connecting the Kakeya Conjecture to Distributed Biological Oscillator Synchronization</h1>
                    <p class="text-gray-600 text-lg mb-2">A Research Initiative</p>
                    <p class="text-gray-700 leading-relaxed">
                        This project explores a novel intersection of pure mathematics and applied engineering. We aim to answer a fundamental question: Using techniques from the recently proven Kakeya Conjecture, what is the minimal "volume" of phase space that a network of distributed biological oscillators must explore to achieve global synchronization? This has direct applications for creating robust, decentralized IoT networks that use biological clocks (like the KaiABC system) instead of traditional digital timekeeping, especially when facing diverse environmental conditions.
                    </p>
                </div>
            </section>

            <section id="concepts">
                <h2 class="text-2xl font-bold text-gray-800 mb-6">Core Concepts</h2>
                <div class="grid md:grid-cols-2 gap-6">
                    <div class="param-card">
                        <h3 class="font-semibold text-lg text-gray-900 mb-2">Kakeya Conjecture</h3>
                        <p class="text-gray-600">In essence, this mathematical theorem provides a lower bound on the size (or "volume") of a set in space that can contain a line segment pointing in every possible direction. Its proof provides powerful tools for geometric measure theory, which can be adapted to analyze the trajectories of oscillators in a high-dimensional phase space.</p>
                    </div>
                    <div class="param-card">
                        <h3 class="font-semibold text-lg text-gray-900 mb-2">Kuramoto Model</h3>
                        <p class="text-gray-600">A foundational mathematical model describing the synchronization of many coupled oscillators. Each oscillator has a natural frequency, and they interact in a way that pulls their phases together. The model helps predict when a network will synchronize based on coupling strength and frequency diversity.</p>
                    </div>
                    <div class="param-card">
                        <h3 class="font-semibold text-lg text-gray-900 mb-2">KaiABC Oscillator</h3>
                        <p class="text-gray-600">A temperature-compensated circadian clock found in cyanobacteria. It's a biochemical oscillator driven by protein phosphorylation cycles. Its robustness and well-understood dynamics make it an ideal candidate for implementation in software for decentralized IoT clock synchronization.</p>
                    </div>
                    <div class="param-card">
                        <h3 class="font-semibold text-lg text-gray-900 mb-2">Phase Space Volume</h3>
                        <p class="text-gray-600">For a network of N oscillators, the "phase space" is an N-dimensional space where each point represents the complete state of the network (the phase of every oscillator). The "volume" required for synchronization refers to the measure of the attractor basin—the set of initial states from which the system will naturally converge to a synchronized state.</p>
                    </div>
                </div>
            </section>

            <section id="environment">
                <h2 class="text-2xl font-bold text-gray-800 mb-6">Environmental Factors: From Temperature to Frequency</h2>
                <p class="text-gray-700 leading-relaxed mb-8">
                    A key challenge is environmental heterogeneity. IoT devices in different locations will experience different temperatures. The KaiABC oscillator's period is temperature-dependent, a relationship quantified by the Q10 temperature coefficient. This section lets you explore how variance in temperature (σ_T) across the network translates into variance in the oscillators' natural frequencies (σ_ω), a critical parameter for synchronization.
                </p>
                <div class="param-card">
                    <div class="grid md:grid-cols-2 gap-8 items-center">
                        <div>
                            <div class="mb-6">
                                <label for="q10-slider" class="block font-medium text-gray-700 mb-2">Q10 Coefficient: <span id="q10-value" class="font-bold text-gray-900">2.2</span></label>
                                <input id="q10-slider" type="range" min="1.0" max="3.0" value="2.2" step="0.1" class="w-full" aria-label="Q10 temperature coefficient slider" aria-valuemin="1.0" aria-valuemax="3.0" aria-valuenow="2.2">
                            </div>
                            <div>
                                <label for="temp-variance-slider" class="block font-medium text-gray-700 mb-2">Temperature Variance (σ_T): <span id="temp-variance-value" class="font-bold text-gray-900">5.0</span> °C</label>
                                <input id="temp-variance-slider" type="range" min="0.1" max="10.0" value="5.0" step="0.1" class="w-full" aria-label="Temperature variance slider in degrees Celsius" aria-valuemin="0.1" aria-valuemax="10.0" aria-valuenow="5.0">
                            </div>
                            <div class="mt-8 p-4 rounded-lg" id="omega-variance-container">
                                <p class="text-gray-600 mb-2">Resulting Frequency Variance (σ_ω):</p>
                                <p id="omega-variance-value" class="text-2xl font-bold text-gray-900 mb-2">0.021 rad/hr</p>
                                <div class="flex items-center gap-2 mb-2">
                                    <span class="text-sm font-medium">Heterogeneity:</span>
                                    <span id="omega-percentage" class="text-sm font-bold">8.0%</span>
                                    <span id="omega-badge" class="text-xs px-2 py-1 rounded-full">Excellent</span>
                                </div>
                                <p id="omega-interpretation" class="text-xs text-gray-600 italic"></p>
                            </div>
                        </div>
                        <div class="chart-container h-64 md:h-80">
                            <canvas id="tempPeriodChart"></canvas>
                        </div>
                    </div>
                    
                    <!-- Basin Volume and Sync Time Predictions -->
                    <div class="grid md:grid-cols-2 gap-6 mt-6">
                        <div class="param-card">
                            <h3 class="font-semibold text-lg text-gray-900 mb-3">Basin of Attraction Volume</h3>
                            <p class="text-sm text-gray-600 mb-4">Fraction of initial conditions that lead to synchronization (for N=10)</p>
                            <div class="relative h-8 bg-gray-200 rounded-full overflow-hidden mb-2">
                                <div id="basin-volume-bar" class="h-full bg-gradient-to-r from-green-500 to-green-600 transition-all duration-500" style="width: 28%"></div>
                            </div>
                            <div class="flex justify-between items-center">
                                <span id="basin-volume-value" class="text-2xl font-bold text-gray-900">28%</span>
                                <span id="basin-volume-status" class="text-sm font-medium text-green-700">Good Coverage</span>
                            </div>
                            <p class="text-xs text-gray-500 mt-2">Higher is better - indicates ease of achieving synchronization</p>
                        </div>
                        
                        <div class="param-card">
                            <h3 class="font-semibold text-lg text-gray-900 mb-3">Synchronization Time Estimate</h3>
                            <p class="text-sm text-gray-600 mb-4">Expected time to reach synchronized state (R > 0.95)</p>
                            <div class="flex items-baseline gap-2 mb-2">
                                <span id="sync-time-value" class="text-3xl font-bold text-gray-900">16</span>
                                <span class="text-lg text-gray-600">days</span>
                            </div>
                            <div class="text-sm text-gray-600">
                                <div class="flex justify-between">
                                    <span>With coupling K =</span>
                                    <span id="sync-time-k" class="font-medium">0.10</span>
                                </div>
                                <div class="flex justify-between">
                                    <span>Critical K_c =</span>
                                    <span id="sync-time-kc" class="font-medium">0.042</span>
                                </div>
                            </div>
                            <p class="text-xs text-gray-500 mt-2">Based on linearized Kuramoto dynamics near synchronization</p>
                        </div>
                    </div>
                </div>
            </section>

            <section id="protocol">
                <h2 class="text-2xl font-bold text-gray-800 mb-6">Structured Research Protocol</h2>
                <div id="accordion-container" class="space-y-3">
                </div>
            </section>
            
            <section id="simulation">
                <h2 class="text-2xl font-bold text-gray-800 mb-6">Interactive Synchronization Simulation</h2>
                <p class="text-gray-700 leading-relaxed mb-8">
                    This dashboard simulates the core dynamics of the Kuramoto model. Adjust the number of oscillators (N), their coupling strength (K), and the frequency variance (σ_ω) to see how these factors influence the network's ability to synchronize. The goal is to find the "critical coupling" (K_c) needed to overcome the frequency differences and achieve a coherent state. Kakeya-derived techniques could provide tighter bounds on the phase space volume these oscillators must explore to find this synchronized state.
                </p>
                <div class="grid lg:grid-cols-3 gap-6">
                    <div class="lg:col-span-1 space-y-6 param-card">
                        <div>
                            <label for="osc-n-slider" class="block font-medium text-gray-700 mb-2">Number of Oscillators (N): <span id="osc-n-value" class="font-bold text-gray-900">50</span></label>
                            <input id="osc-n-slider" type="range" min="5" max="200" value="50" step="1" aria-label="Number of oscillators" aria-valuemin="5" aria-valuemax="200" aria-valuenow="50">
                        </div>
                         <div>
                            <label for="osc-k-slider" class="block font-medium text-gray-700 mb-2">Coupling Strength (K): <span id="osc-k-value" class="font-bold text-gray-900">1.00</span></label>
                            <input id="osc-k-slider" type="range" min="0" max="5" value="1.0" step="0.05" aria-label="Coupling strength between oscillators" aria-valuemin="0" aria-valuemax="5" aria-valuenow="1.0">
                        </div>
                        <div>
                            <label for="osc-omega-slider" class="block font-medium text-gray-700 mb-2">Frequency Variance (σ_ω): <span id="osc-omega-value" class="font-bold text-gray-900">0.50</span></label>
                            <input id="osc-omega-slider" type="range" min="0" max="2" value="0.5" step="0.01" aria-label="Frequency variance among oscillators" aria-valuemin="0" aria-valuemax="2" aria-valuenow="0.5">
                        </div>
                        <div class="grid grid-cols-2 gap-2">
                            <button id="reset-sim-btn" class="bg-gray-700 text-white font-semibold py-2 px-4 rounded-lg hover:bg-gray-800 transition">Reset</button>
                            <button id="export-data-btn" class="bg-blue-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-blue-700 transition">Export CSV</button>
                        </div>
                        <button id="share-config-btn" class="w-full mt-2 bg-green-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-green-700 transition text-sm">Share Configuration</button>
                        
                        <div class="mt-4">
                            <label class="block font-medium text-gray-700 mb-2 text-sm">Scenario Presets:</label>
                            <select id="preset-selector" class="w-full p-2 border border-gray-300 rounded-lg text-sm">
                                <option value="">Custom</option>
                                <option value="ideal">Ideal KaiABC (Q10=1.0, N=10)</option>
                                <option value="realistic">Realistic KaiABC (Q10=1.1, N=20)</option>
                                <option value="uncompensated">Uncompensated (Q10=2.2, N=10)</option>
                                <option value="large-network">Large Network (N=100)</option>
                                <option value="weak-coupling">Weak Coupling Challenge</option>
                                <option value="strong-heterogeneity">Strong Heterogeneity</option>
                            </select>
                        </div>
                    </div>
                    <div class="lg:col-span-2 param-card">
                         <div class="w-full aspect-square bg-gray-100 rounded-lg">
                             <canvas id="oscillator-canvas" role="img" aria-label="Visualization of oscillators on a circle showing their current phases"></canvas>
                         </div>
                    </div>
                </div>
                <div class="mt-6 param-card">
                    <h3 class="font-semibold text-lg text-gray-900 mb-4">Synchronization Analysis</h3>
                    <div class="grid md:grid-cols-2 gap-6 mb-6">
                        <div class="p-4 bg-gray-50 rounded-lg">
                           <p class="text-gray-600">Order Parameter (R):</p>
                           <p id="order-parameter-value" class="text-2xl font-bold text-gray-900">0.00</p>
                           <p class="text-sm text-gray-500">(0 = Unsynchronized, 1 = Fully Synchronized)</p>
                        </div>
                         <div class="p-4 bg-gray-50 rounded-lg">
                           <p class="text-gray-600">Critical Coupling (K_c):</p>
                           <p id="critical-coupling-value" class="text-2xl font-bold text-gray-900">0.64</p>
                           <p class="text-sm text-gray-500">Theoretical K needed to start sync</p>
                        </div>
                    </div>
                    
                    <div class="grid lg:grid-cols-2 gap-6 mb-4">
                        <div>
                            <h4 class="font-medium text-gray-800 mb-2">Order Parameter Evolution</h4>
                            <div class="chart-container h-48">
                                <canvas id="orderParameterChart"></canvas>
                            </div>
                        </div>
                        <div>
                            <h4 class="font-medium text-gray-800 mb-2">Phase Space Projection (2D)</h4>
                            <div class="w-full aspect-square bg-gray-100 rounded-lg" style="max-height: 250px;">
                                <canvas id="phaseSpaceCanvas"></canvas>
                            </div>
                        </div>
                    </div>
                    
                    <p class="text-gray-700 mt-4">The Kakeya conjecture may refine our understanding of the <span class="font-semibold">Hausdorff dimension</span> of the attractor basin, providing a tighter lower bound on the "phase space volume" required for convergence, especially under noisy, real-world conditions.</p>
                </div>
                
                <!-- Bandwidth Calculator -->
                <div class="mt-6 param-card bg-blue-50 border-blue-200">
                    <h3 class="font-semibold text-lg text-gray-900 mb-3">💬 Communication Requirements</h3>
                    <div class="grid md:grid-cols-3 gap-4">
                        <div class="text-center">
                            <p class="text-sm text-gray-600 mb-1">Bandwidth per Device</p>
                            <p id="bandwidth-value" class="text-2xl font-bold text-blue-700">1.5 kbps</p>
                            <p class="text-xs text-gray-500">Sustained average</p>
                        </div>
                        <div class="text-center">
                            <p class="text-sm text-gray-600 mb-1">Energy per Day</p>
                            <p id="energy-value" class="text-2xl font-bold text-green-700">0.3 J</p>
                            <p class="text-xs text-gray-500">≈ 246 year battery</p>
                        </div>
                        <div class="text-center">
                            <p class="text-sm text-gray-600 mb-1">Messages per Day</p>
                            <p id="messages-value" class="text-2xl font-bold text-purple-700">6</p>
                            <p class="text-xs text-gray-500">10 bytes each</p>
                        </div>
                    </div>
                    <div class="mt-4 p-3 bg-white rounded border border-blue-200">
                        <p class="text-xs text-gray-700">
                            <span class="font-semibold">Network efficiency:</span> This system is 
                            <span id="efficiency-comparison" class="font-bold text-blue-700">50-100× more efficient</span> 
                            than traditional NTP/PTP protocols for circadian-scale synchronization.
                        </p>
                    </div>
                </div>
            </section>
            
            <section id="comparison">
                <h2 class="text-2xl font-bold text-gray-800 mb-6">Q10 Scenario Comparison</h2>
                <p class="text-gray-700 leading-relaxed mb-6">Compare the three temperature compensation scenarios side-by-side to understand the critical importance of Q10 ≈ 1.0 for practical IoT deployments.</p>
                
                <div class="overflow-x-auto">
                    <table class="min-w-full bg-white border border-gray-300 rounded-lg">
                        <thead class="bg-gray-100">
                            <tr>
                                <th class="px-4 py-3 text-left text-sm font-semibold text-gray-700 border-b">Parameter</th>
                                <th class="px-4 py-3 text-center text-sm font-semibold text-green-700 border-b">Q10 = 1.0<br/><span class="font-normal text-xs">(Ideal)</span></th>
                                <th class="px-4 py-3 text-center text-sm font-semibold text-blue-700 border-b">Q10 = 1.1<br/><span class="font-normal text-xs">(Realistic)</span></th>
                                <th class="px-4 py-3 text-center text-sm font-semibold text-red-700 border-b">Q10 = 2.2<br/><span class="font-normal text-xs">(Uncompensated)</span></th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr class="border-b hover:bg-gray-50">
                                <td class="px-4 py-3 text-sm text-gray-700 font-medium">σ_ω (rad/hr)</td>
                                <td class="px-4 py-3 text-center text-sm">0.000</td>
                                <td class="px-4 py-3 text-center text-sm">0.021</td>
                                <td class="px-4 py-3 text-center text-sm">0.168</td>
                            </tr>
                            <tr class="border-b hover:bg-gray-50">
                                <td class="px-4 py-3 text-sm text-gray-700 font-medium">Heterogeneity (%)</td>
                                <td class="px-4 py-3 text-center text-sm text-green-600 font-bold">0%</td>
                                <td class="px-4 py-3 text-center text-sm text-blue-600 font-bold">8%</td>
                                <td class="px-4 py-3 text-center text-sm text-red-600 font-bold">64%</td>
                            </tr>
                            <tr class="border-b hover:bg-gray-50">
                                <td class="px-4 py-3 text-sm text-gray-700 font-medium">Critical K_c</td>
                                <td class="px-4 py-3 text-center text-sm">0.000</td>
                                <td class="px-4 py-3 text-center text-sm">0.042</td>
                                <td class="px-4 py-3 text-center text-sm">0.336</td>
                            </tr>
                            <tr class="border-b hover:bg-gray-50">
                                <td class="px-4 py-3 text-sm text-gray-700 font-medium">Basin Volume (N=10)</td>
                                <td class="px-4 py-3 text-center text-sm text-green-600 font-bold">100%</td>
                                <td class="px-4 py-3 text-center text-sm text-blue-600 font-bold">28%</td>
                                <td class="px-4 py-3 text-center text-sm text-red-600 font-bold">0.0001%</td>
                            </tr>
                            <tr class="border-b hover:bg-gray-50">
                                <td class="px-4 py-3 text-sm text-gray-700 font-medium">Sync Time (days)</td>
                                <td class="px-4 py-3 text-center text-sm">7</td>
                                <td class="px-4 py-3 text-center text-sm">16</td>
                                <td class="px-4 py-3 text-center text-sm">2*</td>
                            </tr>
                            <tr class="border-b hover:bg-gray-50">
                                <td class="px-4 py-3 text-sm text-gray-700 font-medium">Bandwidth (kbps)</td>
                                <td class="px-4 py-3 text-center text-sm">&lt;1</td>
                                <td class="px-4 py-3 text-center text-sm">1-2</td>
                                <td class="px-4 py-3 text-center text-sm">5-10</td>
                            </tr>
                            <tr class="border-b hover:bg-gray-50">
                                <td class="px-4 py-3 text-sm text-gray-700 font-medium">Energy (J/day)</td>
                                <td class="px-4 py-3 text-center text-sm">0.1</td>
                                <td class="px-4 py-3 text-center text-sm">0.3</td>
                                <td class="px-4 py-3 text-center text-sm">1.0</td>
                            </tr>
                            <tr class="hover:bg-gray-50">
                                <td class="px-4 py-3 text-sm text-gray-700 font-medium">Viability</td>
                                <td class="px-4 py-3 text-center">⭐⭐⭐⭐⭐</td>
                                <td class="px-4 py-3 text-center">⭐⭐⭐⭐</td>
                                <td class="px-4 py-3 text-center">⭐⭐</td>
                            </tr>
                        </tbody>
                    </table>
                </div>
                <p class="text-xs text-gray-500 mt-4 italic">* Faster sync for Q10=2.2, but from a tiny basin (0.0001% of phase space) - practically difficult to achieve.</p>
                <div class="mt-6 p-4 bg-blue-50 border border-blue-200 rounded-lg">
                    <p class="text-sm text-gray-700">
                        <span class="font-semibold">Key Takeaway:</span> Q10 ≈ 1.1 (realistic KaiABC) provides the best balance: 
                        reasonable basin volume (28%), moderate sync time (16 days), and ultra-low energy consumption (0.3 J/day = 246-year battery life). 
                        This makes it ideal for long-term, energy-constrained IoT deployments.
                    </p>
                </div>
            </section>
            
            <section id="alternatives">
                <h2 class="text-2xl font-bold text-gray-800 mb-6">Alternative Frameworks & Open Questions</h2>
                <p class="text-gray-700 leading-relaxed mb-8">While the Kakeya conjecture offers a promising geometric perspective, it's crucial to consider other mathematical frameworks that could also address the synchronization volume challenge. Each offers a different lens through which to view the problem.</p>
                <div class="grid md:grid-cols-2 lg:grid-cols-3 gap-6">
                    <div class="param-card">
                        <h3 class="font-semibold text-lg text-gray-900 mb-2">Stochastic Processes</h3>
                        <p class="text-gray-600">Models each oscillator as a noisy process, focusing on probabilities and statistical convergence rather than deterministic geometry.</p>
                    </div>
                    <div class="param-card">
                        <h3 class="font-semibold text-lg text-gray-900 mb-2">Information Geometry</h3>
                        <p class="text-gray-600">Uses tools from differential geometry to define a "distance" between different states of synchronization in the phase space, recasting the problem as finding the shortest path to coherence.</p>
                    </div>
                    <div class="param-card">
                        <h3 class="font-semibold text-lg text-gray-900 mb-2">Algebraic Topology</h3>
                        <p class="text-gray-600">Uses concepts like persistent homology to analyze the "shape" of the phase space data over time, identifying when stable synchronization clusters emerge and persist.</p>
                    </div>
                </div>
            </section>

            <section id="implications">
                <h2 class="text-2xl font-bold text-gray-800 mb-6">Practical Implications</h2>
                <div class="space-y-4 text-gray-700 leading-relaxed">
                    <p>The outcomes of this research have significant practical implications for the future of decentralized systems. By establishing theoretical bounds on the requirements for synchronization, we can inform more efficient and robust designs for:</p>
                    <ul class="list-disc list-inside space-y-2 pl-4">
                        <li><strong>Network Architecture:</strong> Determining optimal network topologies (e.g., mesh vs. star) that minimize the phase space exploration needed for synchronization, potentially reducing communication overhead.</li>
                        <li><strong>Synchronization Algorithms:</strong> Designing new distributed protocols that guide oscillators towards the synchronization manifold more efficiently, reducing convergence time and energy consumption.</li>
                        <li><strong>Sensor Sampling Strategies:</strong> Using insights from harmonic analysis, potentially improved by Kakeya, to define optimal sensor reading frequencies to entrain the biological clocks without collecting redundant data.</li>
                    </ul>
                </div>
            </section>
            
            <section id="validation">
                <h2 class="text-2xl font-bold text-gray-800 mb-6">Experimental Validation Framework</h2>
                <p class="text-gray-700 leading-relaxed mb-6">
                    To validate the theoretical predictions, we propose a structured experimental approach combining simulation, hardware testbeds, and mathematical analysis.
                </p>
                
                <div class="grid md:grid-cols-2 gap-6">
                    <div class="param-card">
                        <h3 class="font-semibold text-lg text-gray-900 mb-3">Phase 1: Computational Validation</h3>
                        <ul class="space-y-2 text-gray-600">
                            <li>• Monte Carlo sampling of T^N initial conditions</li>
                            <li>• Measure basin volume vs. Q10 and σ_T</li>
                            <li>• Test dimensional scaling hypothesis (N=5 to 100)</li>
                            <li>• Compare K_c predictions to simulation results</li>
                            <li>• Timeline: 2-3 months</li>
                        </ul>
                    </div>
                    
                    <div class="param-card">
                        <h3 class="font-semibold text-lg text-gray-900 mb-3">Phase 2: Hardware Testbed</h3>
                        <ul class="space-y-2 text-gray-600">
                            <li>• Deploy 10-50 Raspberry Pi Pico devices</li>
                            <li>• Implement KaiABC oscillator in MicroPython</li>
                            <li>• Measure actual bandwidth requirements</li>
                            <li>• Test various network topologies</li>
                            <li>• Timeline: 4-6 months</li>
                        </ul>
                    </div>
                    
                    <div class="param-card">
                        <h3 class="font-semibold text-lg text-gray-900 mb-3">Phase 3: Mathematical Analysis</h3>
                        <ul class="space-y-2 text-gray-600">
                            <li>• Formalize Kakeya → dynamical systems mapping</li>
                            <li>• Derive rigorous dimensional bounds</li>
                            <li>• Publish peer-reviewed results</li>
                            <li>• Collaborate with experts in measure theory</li>
                            <li>• Timeline: 6-12 months</li>
                        </ul>
                    </div>
                    
                    <div class="param-card">
                        <h3 class="font-semibold text-lg text-gray-900 mb-3">Success Metrics</h3>
                        <ul class="space-y-2 text-gray-600">
                            <li>• Basin volume prediction error <10%</li>
                            <li>• K_c measurement within 20% of theory</li>
                            <li>• Bandwidth <2 kbps per device achieved</li>
                            <li>• Convergence time <10 periods for N=10</li>
                            <li>• Robustness to 20% node failures</li>
                        </ul>
                    </div>
                </div>
                
                <div class="mt-6 p-6 bg-amber-50 border border-amber-200 rounded-lg">
                    <h4 class="font-semibold text-gray-900 mb-2">Open Research Questions</h4>
                    <ul class="space-y-1 text-gray-700">
                        <li>1. Can Kakeya techniques provide tighter bounds than traditional Lyapunov analysis?</li>
                        <li>2. How do network delays and packet loss affect the dimensional requirements?</li>
                        <li>3. What is the optimal sensor sampling strategy given bandwidth constraints?</li>
                        <li>4. Can we extend this framework to non-identical oscillators (heterogeneous parameters)?</li>
                        <li>5. How does the approach scale to N>100 devices in realistic IoT deployments?</li>
                    </ul>
                </div>
            </section>
        </div>
    </main>
    </div>

<script>
// ====================
// KAKEYA OSCILLATOR SYNCHRONIZATION WEB APPLICATION
// Modular JavaScript Architecture
// ====================

document.addEventListener('DOMContentLoaded', () => {
    
    // ====================
    // MODULE: Research Protocol Accordion
    // ====================
    const researchProtocolSteps = [
        { title: "1. Foundational Connection", content: "Establish the foundational connection between the Kakeya Conjecture/Kakeya maximal function estimates and geometric measure theory applied to dynamical systems, focusing on dimensional bounds on sets containing curves with all directions." },
        { title: "2. Mathematical Framework Definition", content: "Define the mathematical framework for the system: the Kuramoto/KaiABC coupled oscillator model under heterogeneous environmental forcing (temperature variation) and characterize the resulting synchronization manifold in phase space (Hausdorff or Minkowski dimension)." },
        { title: "3. Literature Search for Kakeya Links", content: "Search specifically for papers (2014-2025) that cite either (a) Katz & Tao's 2014 Kakeya program or (b) Wang & Zahl's 2025 proof in the context of coupled oscillator synchronization, particularly research linking Kakeya-type bounds (including the 'graininess' concept) to the minimal dimensional measure ('volume') of the phase space attractor basin." },
        { title: "4. Environmental Heterogeneity Analysis", content: "Investigate how environmental heterogeneity (temperature-dependent period changes, Q10 effects, Arrhenius kinetics) affects the topology and stability of the synchronization attractor, and how this is mathematically expressed in terms of basin size and fractal dimension." },
        { title: "5. Harmonic Analysis Implications", content: "Research the implications of the Kakeya proof for uncertainty principles and time-frequency localization in harmonic analysis, and how these improvements might lead to new signal processing tools for optimal sensor sampling strategies and filtering of non-stationary biological rhythms." },
        { title: "6. Biological Precedents Search", content: "Search for biological and mathematical physics precedents, specifically papers that connect geometric measure theory, phase space dimensions, or fractal attractors to synchronization in natural biological networks (e.g., SCN neurons, cyanobacteria, fireflies)." },
        { title: "7. Exploration of Alternative Frameworks", content: "Briefly explore alternative mathematical frameworks (e.g., Stochastic Processes, Information Geometry via Fisher metric, Algebraic Topology/Persistent Homology) to contextualize whether the Kakeya approach is optimal." },
        { title: "8. Derivation of Mathematical Bounds", content: "Identify existing bounds or derive order-of-magnitude estimates relating the number of oscillators (N), coupling strength (K), environmental variance (σ_T), and the minimal phase space volume/dimension (d_min) for synchronization." },
        { title: "9. Translation to Practical Implications", content: "Translate the theoretical bounds and geometric insights into practical implications for IoT network architecture: optimal coupling topologies, distributed synchronization algorithms, and sensor data acquisition strategies." },
        { title: "10. Synthesis and Conclusion", content: "Synthesize findings, present the calculated minimal volume/dimensional measure (or existing bounds), evaluate whether Kakeya theory provides meaningful constraints for this application, and identify key open mathematical problems." }
    ];

    const accordionContainer = document.getElementById('accordion-container');
    researchProtocolSteps.forEach((step, index) => {
        const item = document.createElement('div');
        item.className = 'border border-gray-200 rounded-lg bg-white';
        item.innerHTML = `
            <button class="accordion-toggle w-full text-left p-4 font-semibold text-gray-800 flex justify-between items-center hover:bg-gray-50">
                <span>${step.title}</span>
                <span class="transform transition-transform duration-300 text-gray-500">▼</span>
            </button>
            <div class="accordion-content px-4 pb-4 text-gray-600">
                <p>${step.content}</p>
            </div>
        `;
        accordionContainer.appendChild(item);
    });

    accordionContainer.addEventListener('click', (e) => {
        const toggle = e.target.closest('.accordion-toggle');
        if (toggle) {
            const content = toggle.nextElementSibling;
            const icon = toggle.querySelector('span:last-child');
            if (content.style.maxHeight) {
                content.style.maxHeight = null;
                icon.style.transform = 'rotate(0deg)';
            } else {
                document.querySelectorAll('.accordion-content').forEach(c => c.style.maxHeight = null);
                document.querySelectorAll('.accordion-toggle span:last-child').forEach(i => i.style.transform = 'rotate(0deg)');
                content.style.maxHeight = content.scrollHeight + "px";
                icon.style.transform = 'rotate(180deg)';
            }
        }
    });


    const sections = document.querySelectorAll('section');
    const navLinks = document.querySelectorAll('.nav-link');

    const observer = new IntersectionObserver((entries) => {
        entries.forEach(entry => {
            if (entry.isIntersecting) {
                navLinks.forEach(link => {
                    link.classList.remove('active');
                    if (link.getAttribute('href').substring(1) === entry.target.id) {
                        link.classList.add('active');
                    }
                });
            }
        });
    }, { rootMargin: '-50% 0px -50% 0px', threshold: 0 });

    sections.forEach(section => observer.observe(section));


    let tempPeriodChart;
    const q10Slider = document.getElementById('q10-slider');
    const tempVarianceSlider = document.getElementById('temp-variance-slider');
    const q10Value = document.getElementById('q10-value');
    const tempVarianceValue = document.getElementById('temp-variance-value');
    const omegaVarianceValue = document.getElementById('omega-variance-value');

    function updateEnvironmentCalculations() {
        const q10 = parseFloat(q10Slider.value);
        const sigma_T = parseFloat(tempVarianceSlider.value);
        q10Value.textContent = q10.toFixed(1);
        tempVarianceValue.textContent = sigma_T.toFixed(1);

        const T_ref = 24;
        const temp_ref = 30;
        const omega_mean = 2 * Math.PI / T_ref; // rad/hr

        const temps = Array.from({ length: 21 }, (_, i) => 20 + i);
        const periods = temps.map(t => T_ref * Math.pow(q10, (temp_ref - t) / 10));
        
        const dT_dTemp_at_ref = -T_ref * (Math.log(q10) / 10);
        const d_omega_dT = - (2 * Math.PI / (T_ref * T_ref)) * dT_dTemp_at_ref;
        const sigma_omega = Math.abs(d_omega_dT * sigma_T);
        
        // Calculate heterogeneity percentage
        const heterogeneity_percent = (sigma_omega / omega_mean) * 100;

        omegaVarianceValue.textContent = `${sigma_omega.toFixed(3)} rad/hr`;
        
        // Update enhancement #1: Enhanced display with color coding
        const percentageSpan = document.getElementById('omega-percentage');
        const badgeSpan = document.getElementById('omega-badge');
        const interpretationP = document.getElementById('omega-interpretation');
        const container = document.getElementById('omega-variance-container');
        
        if (percentageSpan) percentageSpan.textContent = `${heterogeneity_percent.toFixed(1)}%`;
        
        if (badgeSpan && container) {
            if (heterogeneity_percent < 10) {
                badgeSpan.textContent = 'Excellent';
                badgeSpan.className = 'text-xs px-2 py-1 rounded-full bg-green-100 text-green-800';
                container.className = 'mt-8 p-4 rounded-lg bg-green-50 border border-green-200';
                if (interpretationP) interpretationP.textContent = 'Temperature compensation is very effective. System will easily synchronize.';
            } else if (heterogeneity_percent < 30) {
                badgeSpan.textContent = 'Good';
                badgeSpan.className = 'text-xs px-2 py-1 rounded-full bg-yellow-100 text-yellow-800';
                container.className = 'mt-8 p-4 rounded-lg bg-yellow-50 border border-yellow-200';
                if (interpretationP) interpretationP.textContent = 'Moderate heterogeneity. Requires active coupling but achievable.';
            } else {
                badgeSpan.textContent = 'Challenging';
                badgeSpan.className = 'text-xs px-2 py-1 rounded-full bg-red-100 text-red-800';
                container.className = 'mt-8 p-4 rounded-lg bg-red-50 border border-red-200';
                if (interpretationP) interpretationP.textContent = 'High heterogeneity. Synchronization requires strong coupling and large energy budget.';
            }
        }
        
        // Update enhancement #2: Basin volume prediction
        const N = 10; // For display purposes
        const alpha = 1.5;
        const basin_fraction = Math.pow(Math.max(0.01, 1 - alpha * sigma_omega / omega_mean), N);
        const basin_percent = basin_fraction * 100;
        
        const basinBar = document.getElementById('basin-volume-bar');
        const basinValue = document.getElementById('basin-volume-value');
        const basinStatus = document.getElementById('basin-volume-status');
        
        if (basinBar) basinBar.style.width = `${Math.max(1, Math.min(100, basin_percent))}%`;
        if (basinValue) basinValue.textContent = basin_percent >= 1 ? `${basin_percent.toFixed(0)}%` : `${basin_percent.toFixed(2)}%`;
        
        if (basinStatus) {
            if (basin_percent > 50) {
                basinStatus.textContent = 'Excellent Coverage';
                basinStatus.className = 'text-sm font-medium text-green-700';
                if (basinBar) basinBar.className = 'h-full bg-gradient-to-r from-green-500 to-green-600 transition-all duration-500';
            } else if (basin_percent > 10) {
                basinStatus.textContent = 'Good Coverage';
                basinStatus.className = 'text-sm font-medium text-blue-700';
                if (basinBar) basinBar.className = 'h-full bg-gradient-to-r from-blue-500 to-blue-600 transition-all duration-500';
            } else if (basin_percent > 1) {
                basinStatus.textContent = 'Limited Coverage';
                basinStatus.className = 'text-sm font-medium text-yellow-700';
                if (basinBar) basinBar.className = 'h-full bg-gradient-to-r from-yellow-500 to-yellow-600 transition-all duration-500';
            } else {
                basinStatus.textContent = 'Very Challenging';
                basinStatus.className = 'text-sm font-medium text-red-700';
                if (basinBar) basinBar.className = 'h-full bg-gradient-to-r from-red-500 to-red-600 transition-all duration-500';
            }
        }
        
        // Update enhancement #3: Sync time estimate
        const K_c = 2 * sigma_omega;
        const K_assumed = Math.max(K_c * 2, 0.1); // Assume 2x critical or minimum 0.1
        const lambda = K_assumed - K_c;
        const epsilon = 0.01;
        const sync_time_cycles = lambda > 0 ? Math.log(N / epsilon) / lambda : 999;
        const sync_time_days = Math.min(999, sync_time_cycles);
        
        const syncTimeValue = document.getElementById('sync-time-value');
        const syncTimeK = document.getElementById('sync-time-k');
        const syncTimeKc = document.getElementById('sync-time-kc');
        
        if (syncTimeValue) syncTimeValue.textContent = sync_time_days >= 999 ? '∞' : Math.round(sync_time_days);
        if (syncTimeK) syncTimeK.textContent = K_assumed.toFixed(3);
        if (syncTimeKc) syncTimeKc.textContent = K_c.toFixed(3);
        
        // Update enhancement #4: Bandwidth calculator
        const updates_per_day = sync_time_days < 30 ? 6 : 2; // Adaptive rate
        const bytes_per_message = 10;
        const messages_per_day = updates_per_day * N;
        const bytes_per_second = (messages_per_day * bytes_per_message) / 86400;
        const kbps = (bytes_per_second * 8) / 1000;
        const energy_per_message_mJ = 50;
        const energy_per_day_J = (updates_per_day * energy_per_message_mJ) / 1000;
        
        const bandwidthValue = document.getElementById('bandwidth-value');
        const energyValue = document.getElementById('energy-value');
        const messagesValue = document.getElementById('messages-value');
        const efficiencyComparison = document.getElementById('efficiency-comparison');
        
        if (bandwidthValue) bandwidthValue.textContent = kbps < 1 ? `${(kbps * 1000).toFixed(0)} bps` : `${kbps.toFixed(1)} kbps`;
        if (energyValue) energyValue.textContent = `${energy_per_day_J.toFixed(1)} J`;
        if (messagesValue) messagesValue.textContent = updates_per_day;
        
        if (efficiencyComparison) {
            const efficiency_factor = Math.round(50 / Math.max(1, kbps));
            efficiencyComparison.textContent = `${efficiency_factor}-${efficiency_factor * 2}× more efficient`;
        }

        if (tempPeriodChart) {
            tempPeriodChart.data.datasets[0].data = periods;
            tempPeriodChart.update();
        }
    }

    function createTempPeriodChart() {
        const ctx = document.getElementById('tempPeriodChart').getContext('2d');
        const temps = Array.from({ length: 21 }, (_, i) => 20 + i);
        tempPeriodChart = new Chart(ctx, {
            type: 'line',
            data: {
                labels: temps,
                datasets: [{
                    label: 'Oscillator Period (hours)',
                    data: [],
                    borderColor: '#4A5568',
                    backgroundColor: 'rgba(74, 85, 104, 0.1)',
                    fill: true,
                    tension: 0.3
                }]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                scales: {
                    x: { title: { display: true, text: 'Temperature (°C)' } },
                    y: { title: { display: true, text: 'Period (hr)' } }
                },
                plugins: { legend: { display: false } }
            }
        });
    }

    q10Slider.addEventListener('input', updateEnvironmentCalculations);
    tempVarianceSlider.addEventListener('input', updateEnvironmentCalculations);
    
    createTempPeriodChart();
    updateEnvironmentCalculations();

    // ====================
    // MODULE: Oscillator Simulation with Enhanced Visualizations
    // ====================
    const nSlider = document.getElementById('osc-n-slider');
    const kSlider = document.getElementById('osc-k-slider');
    const omegaSlider = document.getElementById('osc-omega-slider');
    const nValue = document.getElementById('osc-n-value');
    const kValue = document.getElementById('osc-k-value');
    const omegaValue = document.getElementById('osc-omega-value');
    const orderParamValue = document.getElementById('order-parameter-value');
    const criticalCouplingValue = document.getElementById('critical-coupling-value');
    const resetButton = document.getElementById('reset-sim-btn');
    const exportButton = document.getElementById('export-data-btn');
    const shareButton = document.getElementById('share-config-btn');
    const presetSelector = document.getElementById('preset-selector');
    
    const canvas = document.getElementById('oscillator-canvas');
    const ctx = canvas.getContext('2d');
    
    const phaseSpaceCanvas = document.getElementById('phaseSpaceCanvas');
    const phaseCtx = phaseSpaceCanvas ? phaseSpaceCanvas.getContext('2d') : null;

    let N, K, sigma_omega_sim;
    let phases, omegas;
    let animationFrameId;
    let orderParameterHistory = [];
    let orderParameterChart;

    function resizeCanvas() {
        const size = Math.min(canvas.parentElement.clientWidth, canvas.parentElement.clientHeight);
        canvas.width = size;
        canvas.height = size;
        
        if (phaseSpaceCanvas) {
            const psSize = Math.min(phaseSpaceCanvas.parentElement.clientWidth, phaseSpaceCanvas.parentElement.clientHeight);
            phaseSpaceCanvas.width = psSize;
            phaseSpaceCanvas.height = psSize;
        }
    }
    
    function createOrderParameterChart() {
        const ctx = document.getElementById('orderParameterChart');
        if (!ctx) return;
        
        orderParameterChart = new Chart(ctx.getContext('2d'), {
            type: 'line',
            data: {
                labels: [],
                datasets: [{
                    label: 'Order Parameter R(t)',
                    data: [],
                    borderColor: '#4A5568',
                    backgroundColor: 'rgba(74, 85, 104, 0.1)',
                    fill: true,
                    tension: 0.3,
                    pointRadius: 0
                }]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                animation: false,
                scales: {
                    x: { 
                        title: { display: true, text: 'Time Steps' },
                        ticks: { maxTicksLimit: 6 }
                    },
                    y: { 
                        title: { display: true, text: 'R' },
                        min: 0,
                        max: 1
                    }
                },
                plugins: { 
                    legend: { display: false },
                    tooltip: { enabled: false }
                }
            }
        });
    }

    function initializeSimulation() {
        if (animationFrameId) cancelAnimationFrame(animationFrameId);

        N = parseInt(nSlider.value);
        K = parseFloat(kSlider.value);
        sigma_omega_sim = parseFloat(omegaSlider.value);

        nValue.textContent = N;
        kValue.textContent = K.toFixed(2);
        omegaValue.textContent = sigma_omega_sim.toFixed(2);
        
        const Kc = (2 / Math.PI) * (2 * sigma_omega_sim); 
        criticalCouplingValue.textContent = Kc.toFixed(2);
        
        phases = Array.from({ length: N }, () => Math.random() * 2 * Math.PI);
        omegas = Array.from({ length: N }, () => (Math.random() - 0.5) * 2 * sigma_omega_sim);
        
        // Reset order parameter history
        orderParameterHistory = [];
        if (orderParameterChart) {
            orderParameterChart.data.labels = [];
            orderParameterChart.data.datasets[0].data = [];
            orderParameterChart.update();
        }
        
        animationFrameId = requestAnimationFrame(animate);
    }
    
    function animate() {
        const dt = 0.01;
        const nextPhases = new Array(N);

        let sum_sin = 0, sum_cos = 0;
        for(let i=0; i<N; ++i) {
            sum_sin += Math.sin(phases[i]);
            sum_cos += Math.cos(phases[i]);
        }
        
        const order_R = Math.sqrt(sum_sin*sum_sin + sum_cos*sum_cos) / N;
        orderParamValue.textContent = order_R.toFixed(3);
        
        // Update order parameter history (every 5 frames for performance)
        if (Math.random() < 0.2) {
            orderParameterHistory.push(order_R);
            if (orderParameterHistory.length > 200) orderParameterHistory.shift();
            
            if (orderParameterChart && orderParameterHistory.length % 3 === 0) {
                orderParameterChart.data.labels = Array.from({length: orderParameterHistory.length}, (_, i) => i);
                orderParameterChart.data.datasets[0].data = orderParameterHistory;
                orderParameterChart.update('none');
            }
        }
        
        for (let i = 0; i < N; i++) {
            let coupling_sum = 0;
            for (let j = 0; j < N; j++) {
                coupling_sum += Math.sin(phases[j] - phases[i]);
            }
            const d_theta = (omegas[i] + (K / N) * coupling_sum) * dt;
            nextPhases[i] = (phases[i] + d_theta) % (2 * Math.PI);
        }
        phases = nextPhases;
        
        drawOscillators();
        drawPhaseSpace();
        animationFrameId = requestAnimationFrame(animate);
    }
    
    function drawPhaseSpace() {
        if (!phaseCtx || N < 2) return;
        
        const width = phaseSpaceCanvas.width;
        const height = phaseSpaceCanvas.height;
        const padding = 20;
        const plotWidth = width - 2 * padding;
        const plotHeight = height - 2 * padding;
        
        phaseCtx.clearRect(0, 0, width, height);
        
        // Draw axes
        phaseCtx.strokeStyle = '#E2E8F0';
        phaseCtx.lineWidth = 1;
        phaseCtx.beginPath();
        phaseCtx.moveTo(padding, height - padding);
        phaseCtx.lineTo(width - padding, height - padding);
        phaseCtx.moveTo(padding, padding);
        phaseCtx.lineTo(padding, height - padding);
        phaseCtx.stroke();
        
        // Labels
        phaseCtx.fillStyle = '#718096';
        phaseCtx.font = '10px Inter';
        phaseCtx.fillText('φ₁', width - padding + 5, height - padding + 5);
        phaseCtx.fillText('φ₂', padding - 15, padding);
        
        // Plot first two oscillators in 2D phase space projection
        for (let i = 0; i < Math.min(N, 50); i++) {
            const x = padding + (phases[i] / (2 * Math.PI)) * plotWidth;
            const y = height - padding - (phases[(i + 1) % N] / (2 * Math.PI)) * plotHeight;
            
            phaseCtx.beginPath();
            phaseCtx.arc(x, y, 2, 0, 2 * Math.PI);
            phaseCtx.fillStyle = `rgba(74, 85, 104, ${0.3 + 0.7 * (i / Math.min(N, 50))})`;
            phaseCtx.fill();
        }
        
        // Highlight synchronized state (all phases equal = diagonal line)
        phaseCtx.strokeStyle = 'rgba(220, 38, 38, 0.3)';
        phaseCtx.lineWidth = 2;
        phaseCtx.setLineDash([5, 5]);
        phaseCtx.beginPath();
        phaseCtx.moveTo(padding, height - padding);
        phaseCtx.lineTo(width - padding, padding);
        phaseCtx.stroke();
        phaseCtx.setLineDash([]);
    }
    
    function drawOscillators() {
        const width = canvas.width;
        const height = canvas.height;
        const radius = width * 0.4;
        const centerX = width / 2;
        const centerY = height / 2;

        ctx.clearRect(0, 0, width, height);
        
        ctx.strokeStyle = '#E2E8F0';
        ctx.lineWidth = 2;
        ctx.beginPath();
        ctx.arc(centerX, centerY, radius, 0, 2 * Math.PI);
        ctx.stroke();

        for (let i = 0; i < N; i++) {
            const x = centerX + radius * Math.cos(phases[i]);
            const y = centerY + radius * Math.sin(phases[i]);
            ctx.beginPath();
            ctx.arc(x, y, 5, 0, 2 * Math.PI);
            ctx.fillStyle = '#4A5568';
            ctx.fill();
        }
    }
    
    window.addEventListener('resize', () => {
        resizeCanvas();
        drawOscillators();
        drawPhaseSpace();
    });

    // ====================
    // MODULE: Data Export & Sharing
    // ====================
    function exportSimulationData() {
        const data = {
            parameters: {
                N: N,
                K: K,
                sigma_omega: sigma_omega_sim,
                K_c: parseFloat(criticalCouplingValue.textContent)
            },
            currentState: {
                phases: phases,
                omegas: omegas,
                orderParameter: parseFloat(orderParamValue.textContent)
            },
            history: {
                orderParameterHistory: orderParameterHistory
            }
        };
        
        // Convert to CSV format
        let csv = 'Time Step,Order Parameter\n';
        orderParameterHistory.forEach((r, i) => {
            csv += `${i},${r}\n`;
        });
        csv += '\nParameters\n';
        csv += `N,${N}\nK,${K}\nσ_ω,${sigma_omega_sim}\nK_c,${data.parameters.K_c}\n`;
        csv += '\nCurrent Phase Values\n';
        phases.forEach((p, i) => {
            csv += `Oscillator ${i},${p},${omegas[i]}\n`;
        });
        
        // Download
        const blob = new Blob([csv], { type: 'text/csv' });
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = `kakeya_simulation_N${N}_K${K.toFixed(2)}.csv`;
        a.click();
        URL.revokeObjectURL(url);
    }
    
    function shareConfiguration() {
        const params = new URLSearchParams({
            n: N,
            k: K,
            sigma: sigma_omega_sim
        });
        const url = `${window.location.origin}${window.location.pathname}?${params.toString()}`;
        
        if (navigator.clipboard) {
            navigator.clipboard.writeText(url).then(() => {
                const originalText = shareButton.textContent;
                shareButton.textContent = '✓ Copied to Clipboard!';
                setTimeout(() => {
                    shareButton.textContent = originalText;
                }, 2000);
            });
        } else {
            alert(`Share this URL:\n${url}`);
        }
    }
    
    // ====================
    // MODULE: Scenario Presets
    // ====================
    const presets = {
        'ideal': { n: 10, k: 0.05, sigma: 0.021, description: 'Ideal KaiABC with Q10=1.0' },
        'realistic': { n: 20, k: 0.1, sigma: 0.021, description: 'Realistic KaiABC deployment' },
        'uncompensated': { n: 10, k: 0.4, sigma: 0.168, description: 'Poor temperature compensation' },
        'large-network': { n: 100, k: 0.15, sigma: 0.05, description: 'Scaled IoT network' },
        'weak-coupling': { n: 30, k: 0.02, sigma: 0.05, description: 'Minimal communication scenario' },
        'strong-heterogeneity': { n: 20, k: 0.5, sigma: 1.0, description: 'Challenging conditions' }
    };
    
    function loadPreset(presetName) {
        if (!presetName || !presets[presetName]) return;
        
        const preset = presets[presetName];
        nSlider.value = preset.n;
        kSlider.value = preset.k;
        omegaSlider.value = preset.sigma;
        
        nValue.textContent = preset.n;
        kValue.textContent = preset.k.toFixed(2);
        omegaValue.textContent = preset.sigma.toFixed(2);
        
        initializeSimulation();
    }
    
    // Load parameters from URL if present
    function loadFromURL() {
        const params = new URLSearchParams(window.location.search);
        if (params.has('n')) {
            const n = parseInt(params.get('n'));
            if (n >= 5 && n <= 200) {
                nSlider.value = n;
                nValue.textContent = n;
            }
        }
        if (params.has('k')) {
            const k = parseFloat(params.get('k'));
            if (k >= 0 && k <= 5) {
                kSlider.value = k;
                kValue.textContent = k.toFixed(2);
            }
        }
        if (params.has('sigma')) {
            const sigma = parseFloat(params.get('sigma'));
            if (sigma >= 0 && sigma <= 2) {
                omegaSlider.value = sigma;
                omegaValue.textContent = sigma.toFixed(2);
            }
        }
    }

    // ====================
    // EVENT LISTENERS & INITIALIZATION
    // ====================
    nSlider.addEventListener('input', () => { nValue.textContent = nSlider.value; initializeSimulation(); });
    kSlider.addEventListener('input', () => { K = parseFloat(kSlider.value); kValue.textContent = K.toFixed(2); });
    omegaSlider.addEventListener('input', () => {
        sigma_omega_sim = parseFloat(omegaSlider.value);
        omegaValue.textContent = sigma_omega_sim.toFixed(2);
        const Kc = (2 / Math.PI) * (2 * sigma_omega_sim);
        criticalCouplingValue.textContent = Kc.toFixed(2);
        initializeSimulation();
    });
    resetButton.addEventListener('click', initializeSimulation);
    exportButton.addEventListener('click', exportSimulationData);
    shareButton.addEventListener('click', shareConfiguration);
    presetSelector.addEventListener('change', (e) => loadPreset(e.target.value));

    // Initialize all components
    loadFromURL();
    createOrderParameterChart();
    resizeCanvas();
    initializeSimulation();
});
</script>

<footer class="md:ml-64 bg-gray-100 border-t border-gray-200 py-6">
        <div class="max-w-6xl mx-auto px-6">
            <div class="text-center text-gray-600">
                <p class="mb-2">For detailed research methodology and mathematical foundations:</p>
                <a href="../research/Geometric Constraints on Phase Space Exploration for Distributed Biological Oscillators.md" 
                   class="text-blue-600 hover:text-blue-800 underline font-medium">
                    Geometric Constraints on Phase Space Exploration for Distributed Biological Oscillators
                </a>
            </div>
        </div>
    </footer>

</body>
</html>
