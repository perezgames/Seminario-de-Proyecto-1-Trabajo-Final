# Seminario-de-Proyecto-1-Trabajo-Final
TechVision RD — Plataforma web de transformación digital para PyMEs dominicanas. Desarrollada con React.js, Node.js y PostgreSQL. Incluye autenticación con Google, cotizador de proyectos y panel de servicios. Proyecto final — Seminario de Proyecto I, UAPA Mayo- Julio 2026.

TechVision RD es una firma de consultoría y desarrollo tecnológico orientada a ofrecer servicios integrales de digitalización, asesoría avanzada y automatización de flujos de trabajo para pequeñas y medianas empresas (PyMEs) en la República Dominicana. La plataforma permite a los usuariProg Disp Moviles Proyecto INV FINALos simular cotizaciones de proyectos, conocer la metodología de trabajo y acceder a información corporativa mediante autenticación segura con Google.

<img width="952" height="306" alt="Screenshot 2026-06-07 213820" src="https://github.com/user-attachments/assets/cdfc02b5-59f1-493c-81dd-e0313d9d456f" />

**Codigo fuente** 

// TechVision RD - Interactive JS Controls, Mode Selector & Firebase Auth (Multi-page Edition)

// Apply theme immediately on load to prevent flash of dark mode
const savedTheme = localStorage.getItem('techvision-theme');
if (savedTheme === 'light') {
    document.body.classList.add('light-mode');
} else {
    document.body.classList.remove('light-mode');
}

// Apply accessibility preferences immediately on load to prevent layout flash
const savedBold = localStorage.getItem('techvision-bold');
if (savedBold === 'true') {
    document.body.classList.add('bold-text-mode');
} else {
    document.body.classList.remove('bold-text-mode');
}

const savedMotion = localStorage.getItem('techvision-motion');
if (savedMotion === 'true') {
    document.body.classList.add('reduce-motion-mode');
} else {
    document.body.classList.remove('reduce-motion-mode');
}

// Firebase Configuration details
const firebaseConfig = {
  projectId: "techvision-rd-100059326",
  appId: "1:789249374221:web:7d3a0a4cd0e7389cf23d44",
  storageBucket: "techvision-rd-100059326.firebasestorage.app",
  authDomain: "techvision-rd-100059326.firebaseapp.com",
  messagingSenderId: "789249374221",
  projectNumber: "789249374221"
};

// Initialize Firebase SDK
firebase.initializeApp(firebaseConfig);
const auth = firebase.auth();

document.addEventListener('DOMContentLoaded', () => {
    // 1. Google Authentication Routing Flow
    const isLoginPage = window.location.href.includes('login.html');
    
    const googleLoginBtn = document.getElementById('google-login-btn');
    const logoutBtn = document.getElementById('logout-btn');
    const userProfilePanel = document.getElementById('user-profile-panel');
    const userPhoto = document.getElementById('user-photo');
    const userNameLabel = document.getElementById('user-name-label');
    const userEmailLabel = document.getElementById('user-email-label');

    // Sign in flow using Google Auth Popup
    if (googleLoginBtn) {
        googleLoginBtn.addEventListener('click', () => {
            const provider = new firebase.auth.GoogleAuthProvider();
            provider.setCustomParameters({ prompt: 'select_account' });
            
            auth.signInWithPopup(provider)
                .catch(error => {
                    console.error("Error signing in with Google:", error);
                    alert("Error al iniciar sesión: " + error.message);
                });
        });
    }

    // Logout flow
    if (logoutBtn) {
        logoutBtn.addEventListener('click', () => {
            auth.signOut()
                .catch(error => {
                    console.error("Error signing out:", error);
                });
        });
    }

    // Monitor Firebase Auth State Changes
    auth.onAuthStateChanged(user => {
        if (user) {
            // User authenticated successfully
            if (isLoginPage) {
                // If logged in and on login page, redirect to home page
                window.location.href = 'index.html';
                return;
            }

            // Load profile credentials
            if (userPhoto) userPhoto.src = user.photoURL || 'https://via.placeholder.com/32';
            if (userNameLabel) userNameLabel.innerText = user.displayName || 'Usuario';
            if (userEmailLabel) userEmailLabel.innerText = user.email || '';
            if (userProfilePanel) userProfilePanel.style.display = 'inline-block';
        } else {
            // User not authenticated or signed out
            if (!isLoginPage) {
                // If not logged in and on any other page, redirect to login page
                window.location.href = 'login.html';
            }
        }
    });

    // Profile Dropdown Toggle Logic
    const profileTrigger = document.getElementById('profile-trigger');
    if (profileTrigger && userProfilePanel) {
        profileTrigger.addEventListener('click', (e) => {
            e.stopPropagation();
            userProfilePanel.classList.toggle('show-dropdown');
        });

        // Close dropdown when clicking outside
        document.addEventListener('click', (e) => {
            if (!userProfilePanel.contains(e.target)) {
                userProfilePanel.classList.remove('show-dropdown');
            }
        });
    }

    // Light/Dark Theme Switch Logic
    const themeToggle = document.getElementById('theme-toggle');
    if (themeToggle) {
        // Set checkbox state based on current theme preference
        themeToggle.checked = (localStorage.getItem('techvision-theme') === 'light');

        themeToggle.addEventListener('change', (e) => {
            if (e.target.checked) {
                document.body.classList.add('light-mode');
                localStorage.setItem('techvision-theme', 'light');
            } else {
                document.body.classList.remove('light-mode');
                localStorage.setItem('techvision-theme', 'dark');
            }
        });
    }

    // Settings Modal Logic
    const configBtn = document.getElementById('config-btn');
    const settingsModal = document.getElementById('settings-modal');
    const closeSettingsBtn = document.getElementById('close-settings-btn');
    const saveSettingsBtn = document.getElementById('save-settings-btn');
    const configBoldToggle = document.getElementById('config-bold-toggle');
    const configMotionToggle = document.getElementById('config-motion-toggle');

    if (configBtn && settingsModal) {
        configBtn.addEventListener('click', (e) => {
            e.stopPropagation();
            
            // Set initial state of checkboxes based on localStorage
            if (configBoldToggle) {
                configBoldToggle.checked = (localStorage.getItem('techvision-bold') === 'true');
            }
            if (configMotionToggle) {
                configMotionToggle.checked = (localStorage.getItem('techvision-motion') === 'true');
            }
            
            settingsModal.style.display = 'flex';
            // Trigger transition opacity
            setTimeout(() => {
                settingsModal.classList.add('active');
            }, 10);

            // Close the profile dropdown
            if (userProfilePanel) {
                userProfilePanel.classList.remove('show-dropdown');
            }
        });
    }

    if (settingsModal) {
        const closeModal = () => {
            settingsModal.classList.remove('active');
            // Hide completely after opacity transition
            setTimeout(() => {
                if (!settingsModal.classList.contains('active')) {
                    settingsModal.style.display = 'none';
                }
            }, 300);
        };

        if (closeSettingsBtn) {
            closeSettingsBtn.addEventListener('click', closeModal);
        }

        // Close modal when clicking outside of the settings-card
        settingsModal.addEventListener('click', (e) => {
            if (e.target === settingsModal) {
                closeModal();
            }
        });

        if (saveSettingsBtn) {
            saveSettingsBtn.addEventListener('click', () => {
                const isBold = configBoldToggle ? configBoldToggle.checked : false;
                const isMotionReduced = configMotionToggle ? configMotionToggle.checked : false;

                // Save to localStorage
                localStorage.setItem('techvision-bold', isBold ? 'true' : 'false');
                localStorage.setItem('techvision-motion', isMotionReduced ? 'true' : 'false');

                // Apply/remove classes to body immediately
                if (isBold) {
                    document.body.classList.add('bold-text-mode');
                } else {
                    document.body.classList.remove('bold-text-mode');
                }

                if (isMotionReduced) {
                    document.body.classList.add('reduce-motion-mode');
                } else {
                    document.body.classList.remove('reduce-motion-mode');
                }

                closeModal();
            });
        }
    }

    // 2. Mode Toggle (Corporate / Academic) Sincronization
    const modeToggle = document.getElementById('mode-toggle');

    if (modeToggle) {
        modeToggle.addEventListener('change', (e) => {
            if (e.target.checked) {
                document.body.classList.add('academic-mode');
                localStorage.setItem('techvision-mode', 'academic');
            } else {
                document.body.classList.remove('academic-mode');
                localStorage.setItem('techvision-mode', 'corporate');
            }
        });
    }

    // Load persisted mode preference and apply
    const savedMode = localStorage.getItem('techvision-mode');
    if (savedMode === 'academic') {
        if (modeToggle) modeToggle.checked = true;
        document.body.classList.add('academic-mode');
    } else {
        if (modeToggle) modeToggle.checked = false;
        document.body.classList.remove('academic-mode');
    }

    // 3. Objectives Switcher Function (Index page only)
    window.switchObjective = (type) => {
        const buttons = document.querySelectorAll('.obj-tab-btn');
        const panels = document.querySelectorAll('.obj-panel');

        buttons.forEach(btn => {
            if (btn.getAttribute('onclick').includes(type)) {
                btn.classList.add('active');
            } else {
                btn.classList.remove('active');
            }
        });

        panels.forEach(panel => {
            if (panel.id === `obj-${type}`) {
                panel.classList.add('active-panel');
            } else {
                panel.classList.remove('active-panel');
            }
        });
    };

    // 4. Business Model Canva Interactive Inspector (Index page only)
    window.showCanvaDetail = (blockKey) => {
        const canvaBox = document.querySelector(`[onclick*="showCanvaDetail('${blockKey}')"]`);
        if (!canvaBox) return;

        const hiddenContent = canvaBox.querySelector('.canva-hidden-content');
        const detailOverlay = document.getElementById('canva-detail-overlay');
        const detailTitle = document.getElementById('canva-detail-title');
        const detailBody = document.getElementById('canva-detail-body');

        // Extract title
        const titleText = canvaBox.querySelector('h4').innerText;

        // Set content and display
        detailTitle.innerText = titleText;
        detailBody.innerHTML = hiddenContent.innerHTML;
        detailOverlay.style.display = 'block';

        // Scroll smoothly to detail block
        detailOverlay.scrollIntoView({ behavior: 'smooth', block: 'nearest' });
    };

    window.closeCanvaDetail = () => {
        const detailOverlay = document.getElementById('canva-detail-overlay');
        if (detailOverlay) detailOverlay.style.display = 'none';
    };

    // 5. Gantt Chart Interactive Detail Drawer (Planificacion page only)
    const ganttActivities = {
        analisis: {
            title: "Fase 1: Análisis de Requisitos",
            weeks: "Semanas 1 y 2",
            list: [
                "Reuniones de diagnóstico digital con los líderes de las PyMEs piloto.",
                "Redacción del Documento de Especificación de Requisitos de Software (SRS).",
                "Estudio formal de viabilidad tecnológica, jurídica (con Daonil Montero) y comercial.",
                "Definición y aprobación del alcance total del proyecto."
            ]
        },
        diseno: {
            title: "Fase 2: Diseño UX/UI & Arquitectura",
            weeks: "Semanas 3 y 4",
            list: [
                "Elaboración de wireframes interactivos y mockup de alta fidelidad en Figma.",
                "Estructuración lógica y física de la base de datos (Modelo Entidad-Relación).",
                "Definición de las APIs de servicios web y el mapa de navegación del usuario.",
                "Presentación y aprobación de las maquetas visuales del sistema."
            ]
        },
        desarrollo: {
            title: "Fase 3: Desarrollo de Software & Automatizaciones",
            weeks: "Semanas 5 a 8",
            list: [
                "Configuración del repositorio del código (GitHub) y canal de DevOps.",
                "Programación de la interfaz de usuario frontend responsiva.",
                "Construcción del motor de base de datos y endpoints de API backend.",
                "Integración con APIs externas (pasarelas de pago y notificaciones automáticas)."
            ]
        },
        pruebas: {
            title: "Fase 4: Control de Calidad (QA) & Validación",
            weeks: "Semanas 9 y 10",
            list: [
                "Ejecución de pruebas de código unitarias, de integración y rendimiento.",
                "Pruebas de diseño adaptativo en dispositivos móviles y de escritorio reales.",
                "Fase UAT: Pruebas directas de aceptación con el usuario PyME piloto.",
                "Revisión de vulnerabilidades y blindaje de seguridad de datos."
            ]
        },
        implementacion: {
            title: "Fase 5: Implementación & Despliegue Cloud",
            weeks: "Semana 11",
            list: [
                "Despliegue final de la infraestructura en producción de la nube (AWS/Vercel).",
                "Migración y limpieza inicial de la base de datos del cliente.",
                "Lanzamiento controlado (Soft Launch) para evaluar estabilidad de carga."
            ]
        },
        entrega: {
            title: "Fase 6: Entrega Final, Capacitación & Cierre",
            weeks: "Semana 12",
            list: [
                "Sesión presencial de capacitación y entrega del manual de usuario.",
                "Entrega oficial de credenciales, accesos del código y documentación del proyecto.",
                "Firma de la conformidad de cierre y contrato de mantenimiento/soporte técnico."
            ]
        }
    };

    window.showGanttDetail = (phaseKey) => {
        const data = ganttActivities[phaseKey];
        if (!data) return;

        const detailCard = document.getElementById('gantt-detail');
        const detailTitle = document.getElementById('gantt-detail-title');
        const detailWeeks = document.getElementById('gantt-detail-weeks');
        const detailList = document.getElementById('gantt-detail-activities');

        if (detailTitle) detailTitle.innerText = data.title;
        if (detailWeeks) detailWeeks.innerText = data.weeks;
        
        if (detailList) {
            detailList.innerHTML = '';
            data.list.forEach(activity => {
                const li = document.createElement('li');
                li.innerText = activity;
                detailList.appendChild(li);
            });
        }

        // Display
        if (detailCard) {
            detailCard.style.display = 'block';
            detailCard.scrollIntoView({ behavior: 'smooth', block: 'nearest' });
        }
    };

    // Auto-select first phase on planificacion page load
    const ganttRow = document.querySelector('.gantt-row');
    if (ganttRow) {
        showGanttDetail('analisis');
    }

    // 7. Interactive Budget Calculator Logic (Presupuesto page only)
    const inputs = {
        devRate: document.getElementById('input-dev-rate'),
        devHours: document.getElementById('input-dev-hours'),
        designHours: document.getElementById('input-design-hours'),
        infraMonths: document.getElementById('input-infra-months'),
        licenseRate: document.getElementById('input-license-rate'),
        qaHours: document.getElementById('input-qa-hours'),
        trainingSessions: document.getElementById('input-training-sessions')
    };

    const labels = {
        devRate: document.getElementById('val-dev-rate'),
        devHours: document.getElementById('val-dev-hours'),
        designHours: document.getElementById('val-design-hours'),
        infraMonths: document.getElementById('val-infra-months'),
        licenseRate: document.getElementById('val-license-rate'),
        qaHours: document.getElementById('val-qa-hours'),
        trainingSessions: document.getElementById('val-training-sessions')
    };

    const outputs = {
        total: document.getElementById('budget-total'),
        breakdownDev: document.getElementById('val-breakdown-dev'),
        breakdownDesign: document.getElementById('val-breakdown-design'),
        breakdownInfra: document.getElementById('val-breakdown-infra'),
        breakdownLicenses: document.getElementById('val-breakdown-licenses'),
        breakdownQA: document.getElementById('val-breakdown-qa'),
        breakdownTraining: document.getElementById('val-breakdown-training'),
        breakdownMaintenance: document.getElementById('val-breakdown-maintenance'),
        
        barDev: document.getElementById('bar-dev'),
        barDesign: document.getElementById('bar-design'),
        barInfra: document.getElementById('bar-infra'),
        barLicenses: document.getElementById('bar-licenses'),
        barQA: document.getElementById('bar-qa'),
        barTraining: document.getElementById('bar-training'),
        barMaintenance: document.getElementById('bar-maintenance')
    };

    function formatCurrency(val) {
        return new Intl.NumberFormat('en-US', { style: 'currency', currency: 'USD' }).format(val);
    }

    function recalculateBudget() {
        if (!inputs.devRate) return;

        // Fetch input values
        const devRate = parseInt(inputs.devRate.value);
        const devHours = parseInt(inputs.devHours.value);
        const designHours = parseInt(inputs.designHours.value);
        const infraMonths = parseInt(inputs.infraMonths.value);
        const licenseRate = parseInt(inputs.licenseRate.value);
        const qaHours = parseInt(inputs.qaHours.value);
        const trainingSessions = parseInt(inputs.trainingSessions.value);

        // Update control value text
        if (labels.devRate) labels.devRate.innerText = `${devRate} USD`;
        if (labels.devHours) labels.devHours.innerText = `${devHours} horas`;
        if (labels.designHours) labels.designHours.innerText = `${designHours} horas`;
        if (labels.infraMonths) labels.infraMonths.innerText = `${infraMonths} meses`;
        if (labels.licenseRate) labels.licenseRate.innerText = `${licenseRate} USD`;
        if (labels.qaHours) labels.qaHours.innerText = `${qaHours} horas`;
        if (labels.trainingSessions) labels.trainingSessions.innerText = `${trainingSessions} sesiones`;

        // Cost Calculation Formulae
        const costDev = devHours * devRate;
        const costDesign = designHours * (devRate * 0.8);
        const costInfra = infraMonths * 40;
        const costLicenses = infraMonths * licenseRate;
        const costQA = qaHours * (devRate * 0.7);
        const costTraining = trainingSessions * 120;
        const costMaintenance = (devRate * 4) * infraMonths;

        const totalCost = costDev + costDesign + costInfra + costLicenses + costQA + costTraining + costMaintenance;

        // Display Totals and Breakdown Text
        if (outputs.total) outputs.total.innerText = formatCurrency(totalCost);
        if (outputs.breakdownDev) outputs.breakdownDev.innerText = formatCurrency(costDev);
        if (outputs.breakdownDesign) outputs.breakdownDesign.innerText = formatCurrency(costDesign);
        if (outputs.breakdownInfra) outputs.breakdownInfra.innerText = formatCurrency(costInfra);
        if (outputs.breakdownLicenses) outputs.breakdownLicenses.innerText = formatCurrency(costLicenses);
        if (outputs.breakdownQA) outputs.breakdownQA.innerText = formatCurrency(costQA);
        if (outputs.breakdownTraining) outputs.breakdownTraining.innerText = formatCurrency(costTraining);
        if (outputs.breakdownMaintenance) outputs.breakdownMaintenance.innerText = formatCurrency(costMaintenance);

        // Update Chart Bars Width based on percentage
        const pDev = totalCost > 0 ? (costDev / totalCost) * 100 : 0;
        const pDesign = totalCost > 0 ? (costDesign / totalCost) * 100 : 0;
        const pInfra = totalCost > 0 ? (costInfra / totalCost) * 100 : 0;
        const pLicenses = totalCost > 0 ? (costLicenses / totalCost) * 100 : 0;
        const pQA = totalCost > 0 ? (costQA / totalCost) * 100 : 0;
        const pTraining = totalCost > 0 ? (costTraining / totalCost) * 100 : 0;
        const pMaintenance = totalCost > 0 ? (costMaintenance / totalCost) * 100 : 0;

        if (outputs.barDev) outputs.barDev.style.width = `${pDev}%`;
        if (outputs.barDesign) outputs.barDesign.style.width = `${pDesign}%`;
        if (outputs.barInfra) outputs.barInfra.style.width = `${pInfra}%`;
        if (outputs.barLicenses) outputs.barLicenses.style.width = `${pLicenses}%`;
        if (outputs.barQA) outputs.barQA.style.width = `${pQA}%`;
        if (outputs.barTraining) outputs.barTraining.style.width = `${pTraining}%`;
        if (outputs.barMaintenance) outputs.barMaintenance.style.width = `${pMaintenance}%`;
    }

    // Check if budget inputs exist, then bind event listeners
    if (inputs.devRate) {
        Object.keys(inputs).forEach(key => {
            if (inputs[key]) {
                inputs[key].addEventListener('input', recalculateBudget);
            }
        });
        recalculateBudget();
    }
});
