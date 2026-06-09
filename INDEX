<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema de Gestión y Asistencia QR</title>
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <script src="https://cdn.jsdelivr.net/npm/sweetalert2@11"></script>
    <script src="https://unpkg.com/html5-qrcode"></script>
</head>
<body class="bg-slate-100 text-slate-800 font-sans min-h-screen">

    <nav class="bg-indigo-600 text-white shadow-md p-4 sticky top-0 z-50">
        <div class="container mx-auto flex flex-col sm:flex-row justify-between items-center gap-4">
            <h1 class="text-xl font-bold tracking-tight">🏫 Control de Estudiantes y Asistencia QR</h1>
            <button onclick="exportarBackup()" class="w-full sm:w-auto bg-emerald-500 hover:bg-emerald-600 text-white font-semibold px-4 py-2 rounded-lg text-sm transition-all shadow-xs">
                💾 Exportar Backup (.js)
            </button>
        </div>
    </nav>

    <main class="container mx-auto p-4 md:p-6 space-y-8">

        <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
            
            <div class="bg-white p-5 rounded-2xl shadow-xs border border-slate-200 lg:col-span-1 flex flex-col justify-between">
                <div>
                    <h2 class="text-lg font-bold text-slate-700 mb-2 flex items-center gap-2">📷 Escáner QR de Entrada</h2>
                    <p class="text-xs text-slate-500 mb-4">Usa la cámara de tu móvil para escanear el DNI del estudiante.</p>
                    <div id="reader" class="w-full bg-slate-50 rounded-xl overflow-hidden border border-dashed border-slate-300"></div>
                </div>
                <div class="mt-4 flex gap-2">
                    <button id="btn-scan-start" onclick="iniciarCamara()" class="flex-1 bg-indigo-600 hover:bg-indigo-700 text-white py-2.5 rounded-xl text-sm font-medium transition">
                        Encender Cámara
                    </button>
                    <button id="btn-scan-stop" onclick="detenerCamara()" class="flex-1 bg-rose-600 hover:bg-rose-700 text-white py-2.5 rounded-xl text-sm font-medium transition hidden">
                        Apagar
                    </button>
                </div>
            </div>

            <div class="bg-white p-5 rounded-2xl shadow-xs border border-slate-200 lg:col-span-2">
                <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-4 border-b border-slate-100 pb-4 mb-4">
                    <div>
                        <h2 class="text-lg font-bold text-slate-700">📅 Calendario de Asistencias</h2>
                        <p class="text-xs text-slate-500">Selecciona una fecha para gestionar el estado diario.</p>
                    </div>
                    <input type="date" id="fechaFiltro" class="border border-slate-300 rounded-xl p-2.5 text-sm bg-slate-50 focus:outline-none focus:ring-2 focus:ring-indigo-500" onchange="cambiarFecha()">
                </div>

                <div class="overflow-x-auto max-h-[300px]">
                    <table class="w-full text-left border-collapse">
                        <thead>
                            <tr class="bg-slate-50 text-slate-500 text-xs uppercase font-bold sticky top-0 border-b border-slate-200">
                                <th class="p-3">Estudiante</th>
                                <th class="p-3 text-center">Estado</th>
                                <th class="p-3 text-center">Gestión</th>
                            </tr>
                        </thead>
                        <tbody id="tablaAsistencias" class="divide-y divide-slate-100 text-sm">
                            </tbody>
                    </table>
                </div>
            </div>
        </div>

        <div class="bg-white p-5 rounded-2xl shadow-xs border border-slate-200">
            <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4 mb-6">
                <div>
                    <h2 class="text-xl font-bold text-slate-800">👥 Registro General de Alumnos</h2>
                    <p class="text-xs text-slate-500">Base de datos de los 20 estudiantes iniciales y nuevos registros.</p>
                </div>
                <button onclick="formularioEstudiante()" class="w-full sm:w-auto bg-indigo-600 hover:bg-indigo-700 text-white px-5 py-2.5 rounded-xl text-sm font-semibold transition shadow-xs flex items-center justify-center gap-2">
                    ➕ Registrar Estudiante
                </button>
            </div>

            <div class="overflow-x-auto rounded-xl border border-slate-200">
                <table class="w-full text-left border-collapse">
                    <thead>
                        <tr class="bg-slate-50 border-b border-slate-200 text-xs font-bold text-slate-600 uppercase tracking-wider">
                            <th class="p-4">DNI</th>
                            <th class="p-4">Nombres y Apellidos</th>
                            <th class="p-4">Género</th>
                            <th class="p-4">Edad</th>
                            <th class="p-4">Celular</th>
                            <th class="p-4 text-center">Acciones</th>
                        </tr>
                    </thead>
                    <tbody id="tablaEstudiantes" class="divide-y divide-slate-200 text-sm">
                        </tbody>
                </table>
            </div>
        </div>

    </main>

    <script>
        // 1. BASE DE DATOS LOCAL INICIAL (20 ESTUDIANTES PREGRABADOS)
        let estudiantes = [
            { dni: "10000001", nombres: "Juan Carlos Pérez", genero: "Masculino", edad: 20, celular: "987654321" },
            { dni: "10000002", nombres: "María Elena Gómez", genero: "Femenino", edad: 19, celular: "912345678" },
            { dni: "10000003", nombres: "Carlos Alberto Ruiz", genero: "Masculino", edad: 21, celular: "923456789" },
            { dni: "10000004", nombres: "Ana Lucía Torres", genero: "Femenino", edad: 22, celular: "934567890" },
            { dni: "10000005", nombres: "Luis Fernando Castro", genero: "Masculino", edad: 18, celular: "945678901" },
            { dni: "10000006", nombres: "Sofia Valentina Díaz", genero: "Femenino", edad: 20, celular: "956789012" },
            { dni: "10000007", nombres: "Diego Alonso Mendoza", genero: "Masculino", edad: 23, celular: "967890123" },
            { dni: "10000008", nombres: "Camila Fernanda Vega", genero: "Femenino", edad: 19, celular: "978901234" },
            { dni: "10000009", nombres: "Mateo Alejandro Ramos", genero: "Masculino", edad: 21, celular: "989012345" },
            { dni: "10000010", nombres: "Valeria Paz Palacios", genero: "Femenino", edad: 20, celular: "990123456" },
            { dni: "10000011", nombres: "Nicolás André Silva", genero: "Masculino", edad: 22, celular: "911223344" },
            { dni: "10000012", nombres: "Gabriela Sofía Luna", genero: "Femenino", edad: 18, celular: "922334455" },
            { dni: "10000013", nombres: "Santiago José Benítez", genero: "Masculino", edad: 20, celular: "933445566" },
            { dni: "10000014", nombres: "Daniela Alejandra Ríos", genero: "Femenino", edad: 21, celular: "944556677" },
            { dni: "10000015", nombres: "Sebastián Arturo Soto", genero: "Masculino", edad: 19, celular: "955667788" },
            { dni: "10000016", nombres: "Andrea Jimena Lara", genero: "Femenino", edad: 22, celular: "966778899" },
            { dni: "10000017", nombres: "Matías Eduardo Navarro", genero: "Masculino", edad: 23, celular: "977889900" },
            { dni: "10000018", nombres: "Luciana Belén Arce", genero: "Femenino", edad: 20, celular: "988990011" },
            { dni: "10000019", nombres: "Álvaro Gabriel Rossini", genero: "Masculino", edad: 19, celular: "999001122" },
            { dni: "10000020", nombres: "Flavia Antonella Méndez", genero: "Femenino", edad: 21, celular: "912123234" }
        ];

        // Estructura para asistencias organizada por fecha: { "AAAA-MM-DD": { "DNI": "Presente" | "Falta" | "Tardanza" } }
        let asistencias = {};
        let html5QrCodeScanner;
        let fechaActual = "";

        window.onload = () => {
            // Inicializar fecha de hoy por defecto
            fechaActual = new Date().toISOString().split('T')[0];
            document.getElementById('fechaFiltro').value = fechaActual;
            
            inicializarFechaEstructura(fechaActual);
            actualizarTablas();
        };

        function inicializarFechaEstructura(fecha) {
            if (!asistencias[fecha]) {
                asistencias[fecha] = {};
                estudiantes.forEach(est => {
                    asistencias[fecha][est.dni] = "Falta"; // Estado base por defecto
                });
            }
        }

        function cambiarFecha() {
            fechaActual = document.getElementById('fechaFiltro').value;
            inicializarFechaEstructura(fechaActual);
            renderAsistencias();
        }

        function actualizarTablas() {
            renderEstudiantes();
            renderAsistencias();
        }

        // 2. RENDER DE INTERFAZ DE USUARIO
        function renderEstudiantes() {
            const tbody = document.getElementById('tablaEstudiantes');
            tbody.innerHTML = estudiantes.map(est => `
                <tr class="hover:bg-slate-50 transition-colors">
                    <td class="p-4 font-mono font-medium text-slate-700">${est.dni}</td>
                    <td class="p-4 font-semibold text-slate-900">${est.nombres}</td>
                    <td class="p-4 text-slate-600">${est.genero}</td>
                    <td class="p-4 text-slate-600">${est.edad}</td>
                    <td class="p-4 text-slate-600">${est.celular}</td>
                    <td class="p-4 flex justify-center gap-1.5">
                        <button onclick="formularioEstudiante('${est.dni}')" class="bg-blue-50 text-blue-600 hover:bg-blue-100 p-2 rounded-lg transition" title="Editar">✏️</button>
                        <button onclick="eliminarEstudiante('${est.dni}')" class="bg-rose-50 text-rose-600 hover:bg-rose-100 p-2 rounded-lg transition" title="Eliminar">🗑️</button>
                        <button onclick="verSimuladorQR('${est.dni}', '${est.nombres}')" class="bg-purple-50 text-purple-600 hover:bg-purple-100 px-2 py-1 rounded-lg text-xs font-bold transition">Generar QR</button>
                    </td>
                </tr>
            `).join('');
        }

        function renderAsistencias() {
            const tbody = document.getElementById('tablaAsistencias');
            tbody.innerHTML = estudiantes.map(est => {
                const estado = asistencias[fechaActual][est.dni] || "Falta";
                let colorBadge = "bg-rose-100 text-rose-700";
                if (estado === "Presente") colorBadge = "bg-emerald-100 text-emerald-700";
                if (estado === "Tardanza") colorBadge = "bg-amber-100 text-amber-700";

                return `
                    <tr class="hover:bg-slate-50">
                        <td class="p-3 font-medium text-slate-700">${est.nombres} <span class="text-xs text-slate-400 font-mono">(${est.dni})</span></td>
                        <td class="p-3 text-center">
                            <span class="px-3 py-1 rounded-full text-xs font-bold ${colorBadge}">${estado}</span>
                        </td>
                        <td class="p-3 text-center">
                            <button onclick="formularioCambiarAsistencia('${est.dni}')" class="text-xs bg-slate-100 hover:bg-slate-200 text-slate-700 font-medium px-2.5 py-1.5 rounded-lg border border-slate-300 transition">
                                Cambiar Estado ⚙️
                            </button>
                        </td>
                    </tr>
                `;
            }).join('');
        }

        // 3. FORMULARIOS INTERACTIVOS (SWEETALERT 2) - GESTIÓN ESTUDIANTES
        async function formularioEstudiante(dniExistente = null) {
            const esEdicion = dniExistente !== null;
            const alumno = esEdicion ? estudiantes.find(e => e.dni === dniExistente) : { dni:'', nombres:'', genero:'Masculino', edad:'', celular:'' };

            const { value: datosFormulario } = await Swal.fire({
                title: esEdicion ? '✏️ Editar Datos del Estudiante' : '➕ Registrar Nuevo Estudiante',
                html: `
                    <input id="swal-dni" class="swal2-input" placeholder="DNI" value="${alumno.dni}" ${esEdicion ? 'disabled style="background:#e2e8f0; color:#64748b;"' : ''}>
                    <input id="swal-nombres" class="swal2-input" placeholder="Nombres y Apellidos" value="${alumno.nombres}">
                    <select id="swal-genero" class="swal2-input">
                        <option value="Masculino" ${alumno.genero === 'Masculino'?'selected':''}>Masculino</option>
                        <option value="Femenino" ${alumno.genero === 'Femenino'?'selected':''}>Femenino</option>
                    </select>
                    <input id="swal-edad" type="number" class="swal2-input" placeholder="Edad" value="${alumno.edad}">
                    <input id="swal-celular" class="swal2-input" placeholder="Número Celular" value="${alumno.celular}">
                `,
                focusConfirm: false,
                showCancelButton: true,
                confirmButtonText: 'Guardar',
                cancelButtonText: 'Cancelar',
                preConfirm: () => {
                    return {
                        dni: document.getElementById('swal-dni').value.trim(),
                        nombres: document.getElementById('swal-nombres').value.trim(),
                        genero: document.getElementById('swal-genero').value,
                        edad: parseInt(document.getElementById('swal-edad').value),
                        celular: document.getElementById('swal-celular').value.trim()
                    }
                }
            });

            if (datosFormulario) {
                if (!datosFormulario.dni || !datosFormulario.nombres || isNaN(datosFormulario.edad)) {
                    return Swal.fire('Campos incompletos', 'Por favor llena correctamente los campos obligatorios.', 'error');
                }

                if (esEdicion) {
                    const indice = estudiantes.findIndex(e => e.dni === dniExistente);
                    estudiantes[indice] = { ...datosFormulario, dni: dniExistente };
                    Swal.fire('¡Actualizado!', 'Los datos se modificaron con éxito.', 'success');
                } else {
                    if (estudiantes.some(e => e.dni === datosFormulario.dni)) {
                        return Swal.fire('DNI duplicado', 'Ya existe un estudiante registrado con ese DNI.', 'error');
                    }
                    estudiantes.push(datosFormulario);
                    asistencias[fechaActual][datosFormulario.dni] = "Falta"; 
                    Swal.fire('¡Registrado!', 'Estudiante añadido correctamente.', 'success');
                }
                actualizarTablas();
            }
        }

        function eliminarEstudiante(dni) {
            Swal.fire({
                title: '¿Estás completamente seguro?',
                text: "Se borrará al estudiante de la lista y sus registros asociados.",
                icon: 'warning',
                showCancelButton: true,
                confirmButtonColor: '#ef4444',
                cancelButtonColor: '#64748b',
                confirmButtonText: 'Sí, eliminar',
                cancelButtonText: 'Cancelar'
            }).then((result) => {
                if (result.isConfirmed) {
                    estudiantes = estudiantes.filter(e => e.dni !== dni);
                    // Limpieza en la asistencia del día
                    delete asistencias[fechaActual][dni];
                    actualizarTablas();
                    Swal.fire('Eliminado', 'El estudiante ha sido removido.', 'success');
                }
            });
        }

        // 4. FORMULARIOS INTERACTIVOS - CONTROL MANUAL DE ASISTENCIAS
        async function formularioCambiarAsistencia(dni) {
            const alumno = estudiantes.find(e => e.dni === dni);
            const estadoActual = asistencias[fechaActual][dni];

            const { value: nuevoEstado } = await Swal.fire({
                title: `Asistencia para ${alumno.nombres}`,
                text: `Fecha evaluada: ${fechaActual}`,
                input: 'radio',
                inputOptions: {
                    'Presente': 'Presente ✅',
                    'Falta': 'Falta ❌',
                    'Tardanza': 'Tardanza ⏳'
                },
                inputValue: estadoActual,
                showCancelButton: true,
                confirmButtonText: 'Actualizar',
                cancelButtonText: 'Cerrar'
            });

            if (nuevoEstado) {
                asistencias[fechaActual][dni] = nuevoEstado;
                renderAsistencias();
                Swal.fire('Modificado', `Estado cambiado a ${nuevoEstado}`, 'success');
            }
        }

        // 5. MÓDULO LECTOR QR INTEGRADOR (CÁMARA DISPOSITIVO MÓVIL)
        function iniciarCamara() {
            document.getElementById('btn-scan-start').classList.add('hidden');
            document.getElementById('btn-scan-stop').classList.remove('hidden');

            html5QrCodeScanner = new Html5Qrcode("reader");
            const config = { fps: 12, qrbox: { width: 220, height: 220 } };

            // Al configurar 'facingMode: "environment"' forzamos la cámara trasera del dispositivo móvil
            html5QrCodeScanner.start(
                { facingMode: "environment" },
                config,
                (textoEscaneado) => {
                    procesarLecturaQR(textoEscaneado.trim());
                },
                (error) => { /* Silenciar escaneos nulos por segundo */ }
            ).catch(err => {
                console.error(err);
                Swal.fire('Error de Acceso', 'Asegúrate de conceder permisos de cámara y usar HTTPS.', 'error');
                detenerCamara();
            });
        }

        function detenerCamara() {
            document.getElementById('btn-scan-start').classList.remove('hidden');
            document.getElementById('btn-scan-stop').classList.add('hidden');
            
            if (html5QrCodeScanner) {
                html5QrCodeScanner.stop().then(() => {
                    document.getElementById('reader').innerHTML = '';
                }).catch(e => console.log("Cámara apagada"));
            }
        }

        function procesarLecturaQR(dniLeido) {
            const alumno = estudiantes.find(e => e.dni === dniLeido);

            if (alumno) {
                if (asistencias[fechaActual][dniLeido] === "Presente") {
                    Swal.fire('Aviso', `${alumno.nombres} ya registró asistencia hoy.`, 'info');
                } else {
                    asistencias[fechaActual][dniLeido] = "Presente";
                    renderAsistencias();
                    Swal.fire({
                        title: '¡Asistencia QR!',
                        text: `✅ Entrada registrada para: ${alumno.nombres}`,
                        icon: 'success',
                        timer: 2000,
                        showConfirmButton: false
                    });
                }
            } else {
                Swal.fire('Desconocido', `El código QR escaneado (DNI: ${dniLeido}) no se encuentra en la nómina de estudiantes.`, 'warning');
            }
        }

        // 6. SIMULADOR DE CÓDIGOS QR (Para pruebas en PC)
        function verSimuladorQR(dni, nombre) {
            const urlQRAPI = `https://api.qrserver.com/v1/create-qr-code/?size=200x200&data=${dni}`;
            Swal.fire({
                title: `Código QR de ${nombre}`,
                text: `Escanea esta imagen con la cámara de tu celular para registrar la asistencia. Contiene el DNI: ${dni}`,
                imageUrl: urlQRAPI,
                imageWidth: 200,
                imageHeight: 200,
                imageAlt: 'Código QR Estudiantil'
            });
        }

        // 7. EXPORTACIÓN DE COPIA DE SEGURIDAD (BACKUP .JS)
        function exportarBackup() {
            const scriptContenido = `/** * BACKUP GENERADO AUTOMÁTICAMENTE EL: ${new Date().toLocaleString()}
 * CARGA ESTE SCRIPT PARA TRANSFERIR O RECUPERAR LOS REGISTROS.
 */

const BACKUP_ESTUDIANTES = ${JSON.stringify(estudiantes, null, 4)};
const BACKUP_ASISTENCIAS = ${JSON.stringify(asistencias, null, 4)};

console.log("Backup cargado correctamente con " + BACKUP_ESTUDIANTES.length + " estudiantes.");
`;

            const blob = new Blob([scriptContenido], { type: 'text/javascript;charset=utf-8;' });
            const url = URL.createObjectURL(blob);
            const link = document.createElement("a");
            
            link.href = url;
            link.download = `backup_sistema_escolar_${fechaActual}.js`;
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            URL.revokeObjectURL(url);
        }
    </script>
</body>
</html>
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8"><title>Sistema Escolar Simplificado</title>
    <style>
        body { font-family: sans-serif; background: #f0f2f5; padding: 20px; color: #333; }
        .panel { background: #fff; padding: 20px; border-radius: 8px; box-shadow: 0 2px 5px rgba(0,0,0,0.1); max-width: 600px; margin: auto; }
        table { width: 100%; border-collapse: collapse; margin-top: 15px; }
        th, td { border: 1px solid #ddd; padding: 10px; text-align: left; }
        th { background-color: #4f46e5; color: white; }
        button { background: #4f46e5; color: white; border: none; padding: 8px 12px; border-radius: 4px; cursor: pointer; margin-right: 5px; }
        button.danger { background: #dc2626; }
    </style>
</head>
<body>

    <div class="panel">
        <h2>⚡ Control de Alumnos Simplificado</h2>
        <button onclick="registrar()">+ Registrar Estudiante</button>
        <button onclick="descargarBackup()" style="background: #10b981;">💾 Descargar Backup .js</button>
        
        <table>
            <thead>
                <tr><th>DNI</th><th>Estudiante</th><th>Acciones</th></tr>
            </thead>
            <tbody id="tabla-cuerpo"></tbody>
        </table>
    </div>

    <script>
        // Inicializado con los registros requeridos comprimidos
        let alumnos = Array.from({length: 20}, (_, i) => ({
            dni: `100000${String(i+1).padStart(2, '0')}`,
            nombres: `Estudiante Ejemplo Número ${i+1}`
        }));

        function mostrar() {
            const lista = document.getElementById('tabla-cuerpo');
            lista.innerHTML = alumnos.map(a => `
                <tr>
                    <td>${a.dni}</td>
                    <td>${a.nombres}</td>
                    <td>
                        <button onclick="editar('${a.dni}')">✏️</button>
                        <button class="danger" onclick="borrar('${a.dni}')">🗑️</button>
                    </td>
                </tr>
            `).join('');
        }

        function registrar() {
            let dni = prompt("Escribe el DNI del nuevo alumno:");
            let nombres = prompt("Escribe Nombre y Apellido completo:");
            if (dni && nombres) {
                alumnos.push({ dni, nombres });
                mostrar();
            }
        }

        function editar(dni) {
            let alumno = alumnos.find(a => a.dni === dni);
            let nuevoNombre = prompt(`Modificar nombre para el DNI: ${dni}`, alumno.nombres);
            if (nuevoNombre) {
                alumno.nombres = nuevoNombre;
                mostrar();
            }
        }

        function borrar(dni) {
            if (confirm("¿Deseas dar de baja a este estudiante?")) {
                alumnos = alumnos.filter(a => a.dni !== dni);
                mostrar();
            }
        }

        function descargarBackup() {
            let backupTexto = `const backupAlumnos = ${JSON.stringify(alumnos, null, 2)};`;
            let blob = new Blob([backupTexto], {type: "text/javascript"});
            let a = document.createElement("a");
            a.href = URL.createObjectURL(blob);
            a.download = "backup_alumnos.js";
            a.click();
        }

        mostrar(); // Carga inicial de datos
    </script>
</body>
</html>
