# jakubn2020.github.io
<!DOCTYPE html>
<html lang="pl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Centralny Rejestr Lekarzy, Pielęgniarek i Ratowników Medycznych</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <!-- Supabase JS Client -->
    <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
</head>
<body class="bg-gray-100 text-gray-900 font-sans min-h-screen flex flex-col justify-between selection:bg-blue-800 selection:text-white">

    <!-- Header / Government Style Navbar -->
    <header class="bg-[#1e293b] text-white border-b-4 border-[#0f172a]">
        <div class="max-w-5xl mx-auto px-4 py-3 flex flex-col sm:flex-row justify-between items-start sm:items-center">
            <div>
                <h1 class="text-base font-bold tracking-tight uppercase">Centralny Rejestr Lekarzy, Pielęgniarek i Ratowników Medycznych</h1>
                <p class="text-xs text-gray-400">Oficjalny system informacyjny RP (Roleplay)</p>
            </div>
            <div class="mt-2 sm:mt-0">
                <button onclick="openAdminLogin()" class="bg-[#0f172a] hover:bg-black text-gray-200 text-xs font-medium px-3 py-1.5 rounded-sm border border-gray-700 transition">
                    Panel administratora
                </button>
            </div>
        </div>
    </header>

    <!-- Main Content Area -->
    <main class="max-w-3xl mx-auto px-4 py-8 flex-grow w-full">
        
        <!-- Search Section -->
        <div class="bg-white border border-gray-300 p-6 shadow-sm rounded-sm mb-6">
            <h2 class="text-sm font-bold uppercase tracking-wider text-gray-800 mb-3 pb-2 border-b border-gray-200">Wyszukiwanie w rejestrze</h2>
            <p class="text-xs text-gray-600 mb-4">Wpisz numer PWZ, numer PESEL lub pełne imię i nazwisko osoby wykonującej zawód medyczny.</p>
            
            <div class="flex flex-col sm:flex-row gap-2">
                <input type="text" id="searchInput" placeholder="np. 1829384, 92041238491 lub Jan Kowalski" 
                    class="w-full px-3 py-2 bg-gray-50 border border-gray-400 rounded-sm text-sm focus:outline-none focus:ring-1 focus:ring-blue-900 focus:bg-white">
                <button onclick="searchMedyk()" class="bg-[#1e293b] hover:bg-[#0f172a] text-white text-sm font-medium px-5 py-2 rounded-sm transition shrink-0">
                    Szukaj
                </button>
            </div>
        </div>

        <!-- Search Results Container -->
        <div id="resultsContainer" class="space-y-3">
            <div class="bg-white border border-gray-300 p-8 text-center text-gray-500 rounded-sm text-sm">
                Wprowadź kryteria wyszukiwania w formularzu powyżej.
            </div>
        </div>

    </main>

    <!-- Footer -->
    <footer class="bg-gray-200 text-gray-700 text-xs py-4 text-center border-t border-gray-300 mt-12">
        <div class="max-w-5xl mx-auto px-4">
            Centralny rejestr lekarzy, pielęgniarek i ratowników medycznych - strona fikcyjna na potrzeby roleplayu
        </div>
    </footer>

    <!-- Modal Logowania Admina -->
    <div id="adminLoginModal" class="fixed inset-0 bg-black/40 hidden items-center justify-center p-4 z-50">
        <div class="bg-white border border-gray-400 max-w-sm w-full p-5 rounded-sm shadow-md relative">
            <h3 class="text-sm font-bold uppercase tracking-wider text-gray-800 mb-3 pb-2 border-b border-gray-200">Logowanie administratora</h3>
            <div class="space-y-3">
                <div>
                    <label class="block text-xs font-semibold text-gray-700 mb-1">Hasło</label>
                    <input type="password" id="adminPasswordInput" class="w-full px-3 py-1.5 border border-gray-400 rounded-sm text-sm focus:outline-none focus:ring-1 focus:ring-blue-900">
                </div>
                <div class="flex justify-end gap-2 pt-2">
                    <button onclick="closeAdminLogin()" class="bg-gray-200 hover:bg-gray-300 text-gray-800 text-xs font-medium px-3 py-1.5 rounded-sm transition">Anuluj</button>
                    <button onclick="verifyAdmin()" class="bg-[#1e293b] hover:bg-[#0f172a] text-white text-xs font-medium px-4 py-1.5 rounded-sm transition">Wejdź</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Modal Panelu Zarządzania Admina -->
    <div id="adminDashboardModal" class="fixed inset-0 bg-black/50 hidden items-center justify-center p-4 z-50 overflow-y-auto">
        <div class="bg-white border border-gray-400 max-w-4xl w-full p-6 rounded-sm shadow-xl relative my-6 max-h-[90vh] flex flex-col">
            <div class="flex justify-between items-center pb-3 border-b border-gray-200 mb-4">
                <h3 class="text-sm font-bold uppercase tracking-wider text-gray-800">Panel Administratora - Rejestr Medyczny</h3>
                <button onclick="closeAdminDashboard()" class="text-gray-500 hover:text-black font-bold text-sm">✕ Zamknij</button>
            </div>

            <!-- Formularz dodawania / edycji wpisu -->
            <div class="bg-gray-50 border border-gray-300 p-4 rounded-sm mb-4">
                <h4 id="formTitle" class="text-xs font-bold uppercase tracking-wider text-gray-700 mb-3">Dodaj nowy wpis</h4>
                <input type="hidden" id="editId">
                <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-3 mb-3">
                    <div>
                        <label class="block text-xs font-semibold text-gray-700 mb-1">Imię i Nazwisko</label>
                        <input type="text" id="addImieNazwisko" class="w-full px-2.5 py-1.5 bg-white border border-gray-400 rounded-sm text-sm">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-gray-700 mb-1">PESEL (11 cyfr)</label>
                        <input type="text" id="addPesel" maxlength="11" oninput="autoCalculatePWZ()" placeholder="np. 90010112345" class="w-full px-2.5 py-1.5 bg-white border border-gray-400 rounded-sm text-sm">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-gray-700 mb-1">Numer PWZ (Automatyczny)</label>
                        <input type="text" id="addPwz" readonly placeholder="Generuje się z PESEL" class="w-full px-2.5 py-1.5 bg-gray-200 border border-gray-400 rounded-sm text-sm font-mono text-gray-600 cursor-not-allowed">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-gray-700 mb-1">Zawód</label>
                        <select id="addZawod" onchange="handleZawodChange()" class="w-full px-2.5 py-1.5 bg-white border border-gray-400 rounded-sm text-sm">
                            <option value="Lekarz">Lekarz</option>
                            <option value="Pielęgniarka / Pielęgniarz">Pielęgniarka / Pielęgniarz</option>
                            <option value="Ratownik Medyczny">Ratownik Medyczny</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-gray-700 mb-1">Status</label>
                        <select id="addStatus" class="w-full px-2.5 py-1.5 bg-white border border-gray-400 rounded-sm text-sm">
                            <option value="Aktywny">Aktywny</option>
                            <option value="Zawieszony">Zawieszony</option>
                            <option value="Nieaktywny">Nieaktywny</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-gray-700 mb-1">Specjalizacja</label>
                        <input type="text" id="addSpecjalizacja" placeholder="np. Chirurgia ogólna" class="w-full px-2.5 py-1.5 bg-white border border-gray-400 rounded-sm text-sm">
                    </div>
                </div>
                <div class="flex gap-2">
                    <button onclick="saveMedyk()" class="bg-[#1e293b] hover:bg-[#0f172a] text-white text-xs font-medium px-4 py-1.5 rounded-sm transition">Zapisz w bazie</button>
                    <button onclick="resetForm()" id="cancelEditBtn" class="hidden bg-gray-300 hover:bg-gray-400 text-gray-800 text-xs font-medium px-3 py-1.5 rounded-sm transition">Anuluj edycję</button>
                </div>
            </div>

            <!-- Tabela wszystkich wpisów -->
            <div class="overflow-y-auto flex-grow border border-gray-300 rounded-sm">
                <table class="w-full text-left border-collapse text-xs">
                    <thead class="bg-gray-200 text-gray-700 sticky top-0 border-b border-gray-300">
                        <tr>
                            <th class="p-2.5">Imię i Nazwisko</th>
                            <th class="p-2.5">PWZ</th>
                            <th class="p-2.5">PESEL</th>
                            <th class="p-2.5">Zawód</th>
                            <th class="p-2.5">Status</th>
                            <th class="p-2.5">Specjalizacja</th>
                            <th class="p-2.5 text-right">Akcje</th>
                        </tr>
                    </thead>
                    <tbody id="adminTableBody" class="divide-y divide-gray-200 bg-white">
                        <!-- Wypełniane dynamicznie -->
                    </tbody>
                </table>
            </div>
        </div>
    </div>

    <!-- Skrypt aplikacyjny -->
    <script>
        // === KONFIGURACJA SUPABASE ===
        const SUPABASE_URL = 'https://TUTAJ_WKLEJ_SWOJ_URL.supabase.co';
        const SUPABASE_ANON_KEY = 'TUTAJ_WKLEJ_SWOJ_ANON_KEY';
        
        const _supabase = supabase.createClient(SUPABASE_URL, SUPABASE_ANON_KEY);
        const ADMIN_PASS = 'admin123';

        // Automatyczne generowanie numeru PWZ z PESEL
        function autoCalculatePWZ() {
            const pesel = document.getElementById('addPesel').value.trim();
            const pwzInput = document.getElementById('addPwz');

            if (pesel.length >= 7) {
                // Ostatnie 7 cyfr numeru PESEL
                let last7 = pesel.slice(-7);
                // Jeśli pierwsza cyfra to '0', zamień na '1'
                if (last7.startsWith('0')) {
                    last7 = '1' + last7.slice(1);
                }
                pwzInput.value = last7;
            } else {
                pwzInput.value = '';
            }
        }

        // Obsługa wyboru zawodu (Ratownik Medyczny ma sztywno "nd.")
        function handleZawodChange() {
            const zawod = document.getElementById('addZawod').value;
            const specInput = document.getElementById('addSpecjalizacja');

            if (zawod === 'Ratownik Medyczny') {
                specInput.value = 'nd.';
                specInput.disabled = true;
                specInput.classList.add('bg-gray-200', 'cursor-not-allowed');
            } else {
                if (specInput.value === 'nd.') specInput.value = '';
                specInput.disabled = false;
                specInput.classList.remove('bg-gray-200', 'cursor-not-allowed');
            }
        }

        // Wyszukiwanie publiczne medyka
        async function searchMedyk() {
            const query = document.getElementById('searchInput').value.trim().toLowerCase();
            const container = document.getElementById('resultsContainer');

            if (!query) {
                container.innerHTML = `<div class="bg-amber-50 border border-amber-200 text-amber-800 p-4 rounded-sm text-xs">Wprowadź kryteria wyszukiwania (PWZ, PESEL lub Imię i Nazwisko).</div>`;
                return;
            }

            container.innerHTML = `<div class="bg-white border border-gray-300 p-6 text-center text-gray-500 rounded-sm text-xs">Trwa wyszukiwanie w centralnym rejestrze...</div>`;

            try {
                const { data, error } = await _supabase.from('medycy').select('*');
                if (error) throw error;

                const filtered = data.filter(m => 
                    m.pwz.toLowerCase().includes(query) ||
                    m.pesel.toLowerCase().includes(query) ||
                    m.imie_nazwisko.toLowerCase().includes(query)
                );

                if (filtered.length === 0) {
                    container.innerHTML = `
                        <div class="bg-white border border-gray-300 p-6 text-center text-gray-600 rounded-sm text-xs">
                            Brak wyników w rejestrze dla zapytania: <b>"${query}"</b>
                        </div>
                    `;
                    return;
                }

                container.innerHTML = '';
                filtered.forEach(m => {
                    let statusBadgeClass = 'bg-green-100 text-green-800 border-green-300';
                    if (m.status === 'Zawieszony') statusBadgeClass = 'bg-amber-100 text-amber-800 border-amber-300';
                    if (m.status === 'Nieaktywny') statusBadgeClass = 'bg-red-100 text-red-800 border-red-300';

                    container.innerHTML += `
                        <div class="bg-white border border-gray-300 p-4 rounded-sm shadow-xs space-y-3">
                            <div class="flex justify-between items-start pb-2 border-b border-gray-200">
                                <div>
                                    <span class="text-[10px] font-bold uppercase tracking-wider text-gray-500">${m.zawod}</span>
                                    <h3 class="text-sm font-bold text-gray-900">${m.imie_nazwisko}</h3>
                                </div>
                                <span class="px-2 py-0.5 rounded-sm text-[11px] font-semibold border ${statusBadgeClass}">
                                    ${m.status}
                                </span>
                            </div>
                            <div class="grid grid-cols-1 sm:grid-cols-3 gap-2 text-xs">
                                <div>
                                    <span class="text-gray-500 block">Numer PWZ:</span>
                                    <span class="font-mono font-semibold text-gray-800">${m.pwz}</span>
                                </div>
                                <div>
                                    <span class="text-gray-500 block">Specjalizacja:</span>
                                    <span class="font-medium text-gray-800">${m.specjalizacja || 'nd.'}</span>
                                </div>
                                <div>
                                    <span class="text-gray-500 block">Weryfikacja:</span>
                                    <span class="text-green-700 font-medium">Uprawnienia aktywne w systemie</span>
                                </div>
                            </div>
                        </div>
                    `;
                });

            } catch (err) {
                console.error(err);
                container.innerHTML = `<div class="bg-red-50 border border-red-200 text-red-800 p-4 rounded-sm text-xs">Błąd połączenia z bazą danych Supabase. Sprawdź klucze konfiguracyjne.</div>`;
            }
        }

        // Modale Administratora
        function openAdminLogin() { document.getElementById('adminLoginModal').classList.remove('hidden'); document.getElementById('adminLoginModal').classList.add('flex'); }
        function closeAdminLogin() { document.getElementById('adminLoginModal').classList.add('hidden'); document.getElementById('adminLoginModal').classList.remove('flex'); }
        function closeAdminDashboard() { document.getElementById('adminDashboardModal').classList.add('hidden'); document.getElementById('adminDashboardModal').classList.remove('flex'); resetForm(); }

        function verifyAdmin() {
            const pass = document.getElementById('adminPasswordInput').value;
            if (pass === ADMIN_PASS) {
                closeAdminLogin();
                document.getElementById('adminPasswordInput').value = '';
                document.getElementById('adminDashboardModal').classList.remove('hidden');
                document.getElementById('adminDashboardModal').classList.add('flex');
                loadAdminTable();
            } else {
                alert('Nieprawidłowe hasło administratora!');
            }
        }

        // Ładowanie tabeli panelu admina
        async function loadAdminTable() {
            const tbody = document.getElementById('adminTableBody');
            tbody.innerHTML = `<tr><td colspan="7" class="p-4 text-center text-gray-500">Pobieranie danych z bazy...</td></tr>`;

            const { data, error } = await _supabase.from('medycy').select('*').order('id', { ascending: false });
            if (error) {
                tbody.innerHTML = `<tr><td colspan="7" class="p-4 text-center text-red-600">Błąd ładowania tabeli.</td></tr>`;
                return;
            }

            if (data.length === 0) {
                tbody.innerHTML = `<tr><td colspan="7" class="p-4 text-center text-gray-500">Brak wpisów w bazie danych.</td></tr>`;
                return;
            }

            tbody.innerHTML = '';
            data.forEach(m => {
                tbody.innerHTML += `
                    <tr class="hover:bg-gray-50">
                        <td class="p-2.5 font-medium text-gray-900">${m.imie_nazwisko}</td>
                        <td class="p-2.5 font-mono text-gray-700">${m.pwz}</td>
                        <td class="p-2.5 font-mono text-gray-700">${m.pesel}</td>
                        <td class="p-2.5 text-gray-700">${m.zawod}</td>
                        <td class="p-2.5"><span class="px-1.5 py-0.5 rounded text-[10px] font-medium ${m.status === 'Aktywny' ? 'bg-green-100 text-green-800' : 'bg-red-100 text-red-800'}">${m.status}</span></td>
                        <td class="p-2.5 text-gray-700">${m.specjalizacja || 'nd.'}</td>
                        <td class="p-2.5 text-right space-x-2">
                            <button onclick="editMedyk(${m.id}, '${m.imie_nazwisko}', '${m.pesel}', '${m.pwz}', '${m.zawod}', '${m.status}', '${m.specjalizacja || ''}')" class="text-blue-700 hover:underline">Edytuj</button>
                            <button onclick="deleteMedyk(${m.id})" class="text-red-700 hover:underline">Usuń</button>
                        </td>
                    </tr>
                `;
            });
        }

        // Zapis / edycja medyka w Supabase
        async function saveMedyk() {
            const id = document.getElementById('editId').value;
            const imie_nazwisko = document.getElementById('addImieNazwisko').value.trim();
            const pesel = document.getElementById('addPesel').value.trim();
            const pwz = document.getElementById('addPwz').value.trim();
            const zawod = document.getElementById('addZawod').value;
            const status = document.getElementById('addStatus').value;
            let specjalizacja = document.getElementById('addSpecjalizacja').value.trim();

            if (!imie_nazwisko || !pesel || !pwz) {
                alert('Proszę wypełnić Imię i Nazwisko oraz PESEL (PWZ wygeneruje się automatycznie).');
                return;
            }

            if (pesel.length !== 11) {
                alert('Numer PESEL musi składać się dokładnie z 11 cyfr!');
                return;
            }

            if (zawod === 'Ratownik Medyczny') {
                specjalizacja = 'nd.';
            }

            const payload = { imie_nazwisko, pesel, pwz, zawod, status, specjalizacja };

            if (id) {
                const { error } = await _supabase.from('medycy').update(payload).eq('id', id);
                if (error) alert('Błąd edycji: ' + error.message);
                else { alert('Wpis zaktualizowany pomyślnie!'); resetForm(); loadAdminTable(); }
            } else {
                const { error } = await _supabase.from('medycy').insert([payload]);
                if (error) alert('Błąd dodawania: ' + error.message);
                else { alert('Dodano nowy wpis do rejestru!'); resetForm(); loadAdminTable(); }
            }
        }

        function editMedyk(id, imieNazwisko, pesel, pwz, zawod, status, specjalizacja) {
            document.getElementById('editId').value = id;
            document.getElementById('addImieNazwisko').value = imieNazwisko;
            document.getElementById('addPesel').value = pesel;
            document.getElementById('addPwz').value = pwz;
            document.getElementById('addZawod').value = zawod;
            document.getElementById('addStatus').value = status;
            
            handleZawodChange();
            if (zawod !== 'Ratownik Medyczny') {
                document.getElementById('addSpecjalizacja').value = specjalizacja;
            }

            document.getElementById('formTitle').textContent = `Edycja wpisu (ID: ${id})`;
            document.getElementById('cancelEditBtn').classList.remove('hidden');
        }

        function resetForm() {
            document.getElementById('editId').value = '';
            document.getElementById('addImieNazwisko').value = '';
            document.getElementById('addPesel').value = '';
            document.getElementById('addPwz').value = '';
            document.getElementById('addZawod').value = 'Lekarz';
            document.getElementById('addStatus').value = 'Aktywny';
            document.getElementById('addSpecjalizacja').value = '';
            handleZawodChange();
            document.getElementById('formTitle').textContent = 'Dodaj nowy wpis';
            document.getElementById('cancelEditBtn').classList.add('hidden');
        }

        async function deleteMedyk(id) {
            if (confirm('Czy na pewno chcesz usunąć ten wpis z rejestru?')) {
                const { error } = await _supabase.from('medycy').delete().eq('id', id);
                if (error) alert('Błąd usuwania: ' + error.message);
                else loadAdminTable();
            }
        }
    </script>
</body>
</html>
