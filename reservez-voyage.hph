<?php
// --- TRAITEMENT PHP POUR XAMPP ---
// Ce code s'exécute en arrière-plan quand le JavaScript envoie les données
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    // 1. Connexion à ta base de données
    $conn = mysqli_connect("localhost", "root", "", "agence_voyage");

    if ($conn) {
        // 2. On récupère les informations du formulaire
        // (PHP remplace automatiquement les espaces par des tirets du bas _)
        $nom = $_POST['Nom_Complet'] ?? '';
        $email = $_POST['Email'] ?? '';
        
        // Pour la destination, on vérifie si le client a choisi "Autre"
        $destination = $_POST['Pays'] ?? '';
        if ($destination === 'Autre') {
            $destination = $_POST['Autre_Pays'] ?? 'Non précisé';
        }

        // Sécurisation des textes
        $nom = mysqli_real_escape_string($conn, $nom);
        $email = mysqli_real_escape_string($conn, $email);
        $destination = mysqli_real_escape_string($conn, $destination);

        // 3. Insertion dans ta table (qui contient id, nom, email, destination)
        $sql = "INSERT INTO reservation (nom, email, destination) VALUES ('$nom', '$email', '$destination')";
        mysqli_query($conn, $sql);
        
        mysqli_close($conn);
    }
    
    // 4. On arrête le script ici pour que le JavaScript affiche l'écran de succès
    http_response_code(200);
    exit;
}
?>

<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Réservez Voyage - Paisible Fly</title>
    <style>
        /* --- VARIABLES & RESET --- */
        :root {
            --primary: #007BFF;
            --primary-hover: #0056b3;
            --secondary: #FF8C00;
            --bg-color: #f4f7f6;
            --text-color: #333;
            --error-color: #dc3545;
            --success-color: #28a745;
            --border-radius: 8px;
        }
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        body { background: transparent; color: var(--text-color); padding: 20px; line-height: 1.6; min-height: 100vh; }

        /* --- ANIMATIONS --- */
        @keyframes slideIn { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

        /* --- LAYOUT --- */
        .container { max-width: 800px; margin: auto; background: white; padding: 30px; border-radius: 15px; box-shadow: 0 10px 30px rgba(0,0,0,0.1); animation: slideIn 0.5s ease-out; }
        
        .header { text-align: center; margin-bottom: 30px; }
        .header h1 { color: var(--primary); font-size: 2.5em; margin-bottom: 10px; }
        .header p { color: #666; font-size: 1.1em; }

        /* --- FORM ELEMENTS --- */
        .section-title { font-size: 1.5em; color: var(--primary); border-bottom: 2px solid var(--primary); padding-bottom: 5px; margin: 30px 0 15px; display: flex; align-items: center; gap: 10px; }
        
        .form-group { margin-bottom: 20px; animation: fadeIn 0.4s ease; }
        label { display: block; font-weight: bold; margin-bottom: 8px; color: #555; }
        input[type="text"], input[type="email"], input[type="tel"], input[type="date"], select {
            width: 100%; padding: 12px; border: 1px solid #ccc; border-radius: var(--border-radius); font-size: 1em; transition: all 0.3s;
        }
        input:focus, select:focus { border-color: var(--primary); outline: none; box-shadow: 0 0 5px rgba(0, 123, 255, 0.3); }
        
        /* Error state */
        .input-error { border-color: var(--error-color) !important; background-color: #fff8f8; box-shadow: 0 0 5px rgba(220, 53, 69, 0.3); }
        .error-msg { color: var(--error-color); font-size: 0.85em; display: none; margin-top: 5px; }
        .input-error + .error-msg { display: block; }

        /* Readonly */
        input[readonly] { background-color: #e9ecef; cursor: not-allowed; }

        /* Radio buttons */
        .radio-group { display: flex; gap: 20px; }
        .radio-group label { font-weight: normal; cursor: pointer; display: flex; align-items: center; gap: 5px; }

        /* Dynamic sections */
        .hidden { display: none !important; }
        .info-msg { background: #e2f0ff; color: #0056b3; padding: 10px; border-left: 4px solid var(--primary); border-radius: 4px; font-size: 0.9em; margin-top: 10px; animation: fadeIn 0.3s ease; }
        .warning-msg { background: #fff3cd; color: #856404; padding: 10px; border-left: 4px solid #ffc107; border-radius: 4px; font-size: 0.9em; margin-top: 10px; animation: fadeIn 0.3s ease; }

        /* Sub-sections (Partner/Children) */
        .sub-section { background: #f8f9fa; border: 1px dashed #ccc; padding: 15px; border-radius: var(--border-radius); margin-top: 15px; position: relative; }
        .child-block { border-bottom: 1px solid #ddd; padding-bottom: 15px; margin-bottom: 15px; }

        /* Buttons */
        .btn { padding: 12px 25px; border: none; border-radius: var(--border-radius); font-size: 1.1em; cursor: pointer; transition: 0.3s; display: inline-flex; align-items: center; justify-content: center; gap: 10px; font-weight: bold; width: 100%; color: white; }
        .btn-submit { background: var(--primary); margin-top: 20px; }
        .btn-submit:hover { background: var(--primary-hover); transform: translateY(-2px); }
        .btn-add { background: var(--secondary); margin-top: 10px; width: auto; font-size: 0.9em; padding: 8px 15px; }
        .btn-add:hover { background: #e67e00; }
        .btn-whatsapp { background: #25D366; }
        .btn-whatsapp:hover { background: #1ebd5a; }
        .btn-print { background: #6c757d; }
        .btn-print:hover { background: #5a6268; }
        .btn-home { background: var(--primary); }
        .btn-home:hover { background: var(--primary-hover); }

        /* Success Screen */
        #success-screen { text-align: center; padding: 40px 20px; }
        #success-screen h2 { color: var(--success-color); margin-bottom: 15px; font-size: 2em; }
        .action-buttons { display: flex; gap: 15px; margin-top: 30px; flex-direction: column; }
        @media(min-width: 600px) { .action-buttons { flex-direction: row; flex-wrap: wrap; justify-content: center; } }

        /* Print formatting */
        @media print {
            body { background: white; padding: 0; }
            .container { box-shadow: none; animation: none; padding: 0; }
            .btn, #success-screen .action-buttons { display: none !important; }
            .header h1 { font-size: 1.8em; }
            #success-screen { display: none !important; }
            #booking-form { display: block !important; }
        }
    </style>
</head>
<body>

<div class="container" id="main-container">
    <div class="header">
        <h1>✈️ Réservez Voyage</h1>
        <p>Remplissez ce formulaire pour réserver votre voyage auprès de notre agence.</p>
    </div>

    <form id="booking-form" action="" method="POST" novalidate>
        
        <h2 class="section-title">👤 Données Personnelles</h2>
        
        <div class="form-group">
            <label>Nom Complet *</label>
            <input type="text" id="nom" name="Nom Complet" placeholder="Ex: Keyna Lundvall" required>
            <div class="error-msg">Ce champ est obligatoire.</div>
        </div>

        <div class="form-group">
            <label>Téléphone *</label>
            <input type="tel" id="tel" name="Téléphone" placeholder="Ex: +243 84 444 4486" required>
            <div class="error-msg">Ce champ est obligatoire.</div>
        </div>

        <div class="form-group">
            <label>WhatsApp *</label>
            <input type="tel" id="whatsapp" name="WhatsApp" placeholder="Ex: +1 (206) 336-3900" required>
            <div class="error-msg">Ce champ est obligatoire.</div>
        </div>

        <div class="form-group">
            <label>E-mail *</label>
            <input type="email" id="email" name="Email" placeholder="Ex: infos@paisiblefly.com" required>
            <div class="error-msg">Ce champ est obligatoire et doit être valide.</div>
        </div>

        <div class="form-group">
            <label>Adresse à domicile *</label>
            <input type="text" id="adresse" name="Adresse" placeholder="Ex: 110, Av. Lokolela, Immeuble Voici l’homme 2ème niveau C/ Kinshasa" required>
            <div class="error-msg">Ce champ est obligatoire.</div>
        </div>

        <div class="form-group">
            <label>Date de naissance *</label>
            <input type="date" id="dob" name="Date de naissance" required>
            <div class="error-msg">Veuillez choisir une date.</div>
        </div>

        <div class="form-group">
            <label>Âge (Calculé automatiquement)</label>
            <input type="text" id="age" name="Age" placeholder="Ex: 23 ans" readonly>
        </div>

        <div class="form-group">
            <label>Genre *</label>
            <select id="genre" name="Genre" required>
                <option value="">Sélectionnez...</option>
                <option value="Masculin">Masculin 👨</option>
                <option value="Féminin">Féminin 👩</option>
            </select>
            <div class="error-msg">Veuillez choisir un genre.</div>
        </div>

        <div class="form-group">
            <label>État Civil *</label>
            <div class="radio-group">
                <label><input type="radio" name="Etat Civil" value="Marié(e)" required> Marié(e) 💍</label>
                <label><input type="radio" name="Etat Civil" value="Célibataire" required> Célibataire 🚶</label>
            </div>
            <div class="error-msg" id="etat-civil-error" style="display:none;">Veuillez choisir un état civil.</div>
        </div>

        <h2 class="section-title">🌍 Détails du Voyage</h2>

        <div class="form-group">
            <label>Pays choisi *</label>
            <select id="pays" name="Pays" required>
                <option value="">Sélectionnez une destination...</option>
                <option value="Canada">Canada 🇨🇦</option>
                <option value="Chine">Chine 🇨🇳</option>
                <option value="Etats-Unis">Etats-Unis 🇺🇸</option>
                <option value="Chypres">Chypres 🇨🇾</option>
                <option value="Russie">Russie 🇷🇺</option>
                <option value="Tunisie">Tunisie 🇹🇳</option>
                <option value="Maroc">Maroc 🇲🇦</option>
                <option value="Australie">Australie 🇦🇺</option>
                <option value="Brésil">Brésil 🇧🇷</option>
                <option value="Espagne">Espagne 🇪🇸</option>
                <option value="Serbie">Serbie 🇷🇸</option>
                <option value="Singapour">Singapour 🇸🇬</option>
                <option value="Malaisie">Malaisie 🇲🇾</option>
                <option value="Macédoine">Macédoine 🇲🇰</option>
                <option value="Philippine">Philippine 🇵🇭</option>
                <option value="Cuba">Cuba 🇨🇺</option>
                <option value="Mexique">Mexique 🇲🇽</option>
                <option value="Dubaï">Dubaï 🇦🇪</option>
                <option value="France">France 🇫🇷</option>
                <option value="Belgique">Belgique 🇧🇪</option>
                <option value="Suisse">Suisse 🇨🇭</option>
                <option value="Londres">Londres (Royaume-Uni) 🇬🇧</option>
                <option value="Autre">Autre 🌐</option>
            </select>
            <div class="error-msg">Veuillez choisir un pays.</div>
            
            <div id="autre-pays-container" class="hidden" style="margin-top: 10px;">
                <input type="text" id="autre-pays" name="Autre Pays" placeholder="Ex: Bolivie">
            </div>
            <div id="msg-pays" class="info-msg hidden">💡 Rassurez-vous que le pays choisi est le même pays que vous avez vu sur l’offre.</div>
        </div>

        <div class="form-group">
            <label>Avez-vous un passeport ? *</label>
            <select id="passeport" name="A le passeport" required>
                <option value="">Sélectionnez...</option>
                <option value="Oui">Oui ✅</option>
                <option value="Non">Non ❌</option>
            </select>
            <div class="error-msg">Veuillez répondre.</div>

            <div id="passeport-oui" class="hidden" style="margin-top: 10px;">
                <input type="text" id="num-passeport" name="Numero Passeport" placeholder="Ex: P000243534">
            </div>
            <div id="passeport-non" class="warning-msg hidden">⚠️ Nous acceptons bien votre réservation mais vous devez commencer par l’obtention de votre document de voyage (Passeport) au sein de notre agence.</div>
        </div>

        <div class="form-group">
            <label>Vous voyagez avec *</label>
            <select id="compagnie" name="Voyage avec" required>
                <option value="">Sélectionnez...</option>
                <option value="Seul(e)">Je voyage seul(e) 🧍</option>
                <option value="Conjoint(e)">Je voyage avec mon/ma conjoint(e) 👫</option>
                <option value="Famille">Je voyage en famille 👨‍👩‍👧‍👦</option>
            </select>
            <div class="error-msg">Veuillez préciser avec qui vous voyagez.</div>
        </div>

        <div id="section-conjoint" class="sub-section hidden">
            <h3>❤️ Informations du Conjoint(e)</h3>
            <div class="form-group" style="margin-top:10px;">
                <label>Nom Complet</label>
                <input type="text" name="Conjoint Nom" placeholder="Ex: Mamie Bokitanya Mputu">
            </div>
            <div class="form-group">
                <label>Date de naissance</label>
                <input type="date" name="Conjoint DOB">
            </div>
            <div class="form-group">
                <label>Téléphone</label>
                <input type="tel" name="Conjoint Tel" placeholder="Ex: +243 84 444 4486">
            </div>
            <div class="form-group">
                <label>A-t-il/elle un passeport ?</label>
                <select id="passeport-conjoint" name="Conjoint Passeport">
                    <option value="">Sélectionnez...</option>
                    <option value="Oui">Oui</option>
                    <option value="Non">Non</option>
                </select>
                <div id="passeport-conjoint-oui" class="hidden" style="margin-top: 10px;">
                    <input type="text" name="Conjoint Num Passeport" placeholder="Ex: P00034354">
                </div>
                <div id="passeport-conjoint-non" class="warning-msg hidden">⚠️ Merci de passer à nos locaux pour commencer la procédure d’obtention de passeport de votre conjoint(e).</div>
            </div>
        </div>

        <div id="section-enfants" class="sub-section hidden">
            <h3>👶 Informations des Enfants</h3>
            <div id="enfants-container">
                </div>
            <button type="button" class="btn btn-add" id="btn-add-enfant">➕ Ajouter un enfant</button>
        </div>

        <button type="submit" class="btn btn-submit">🚀 Soumettre la Réservation</button>
    </form>

    <div id="success-screen" class="hidden">
        <h2>🎉 Félicitations !</h2>
        <p><strong>Merci très cher client, votre réservation a été enregistrée avec succès !</strong></p>
        <p>Notre équipe spécialisée va vous contacter après l’étude de votre demande pour un devis ou une prise de rendez-vous.</p>
        <p style="margin-top: 15px; color: var(--secondary);"><em>Pour plus de rapidité, veuillez confirmer votre demande sur WhatsApp.</em></p>
        
        <div class="action-buttons">
            <button id="btn-go-whatsapp" class="btn btn-whatsapp">💬 Confirmer sur WhatsApp</button>
            <button id="btn-imprimer" class="btn btn-print">🖨️ Imprimer la réservation</button>
            <button onclick="window.location.href='index.html'" class="btn btn-home">🏠 Retour à l'accueil</button>
        </div>
    </div>

</div>

<script>
    document.addEventListener("DOMContentLoaded", () => {
        
        // --- CALCUL DE L'ÂGE ---
        document.getElementById('dob').addEventListener('change', function() {
            if(!this.value) return;
            const birthDate = new Date(this.value);
            const today = new Date();
            let age = today.getFullYear() - birthDate.getFullYear();
            const m = today.getMonth() - birthDate.getMonth();
            if (m < 0 || (m === 0 && today.getDate() < birthDate.getDate())) { age--; }
            document.getElementById('age').value = age + ' ans';
        });

        // --- GESTION DES PAYS ---
        document.getElementById('pays').addEventListener('change', function() {
            const autreContainer = document.getElementById('autre-pays-container');
            const msgPays = document.getElementById('msg-pays');
            
            if (this.value === "Autre") {
                autreContainer.classList.remove('hidden');
                msgPays.classList.remove('hidden');
            } else if (this.value !== "") {
                autreContainer.classList.add('hidden');
                msgPays.classList.remove('hidden');
            } else {
                autreContainer.classList.add('hidden');
                msgPays.classList.add('hidden');
            }
        });

        // --- GESTION PASSEPORT PRINCIPAL ---
        document.getElementById('passeport').addEventListener('change', function() {
            const ouiDiv = document.getElementById('passeport-oui');
            const nonDiv = document.getElementById('passeport-non');
            ouiDiv.classList.add('hidden');
            nonDiv.classList.add('hidden');
            if (this.value === "Oui") ouiDiv.classList.remove('hidden');
            if (this.value === "Non") nonDiv.classList.remove('hidden');
        });

        // --- GESTION PASSEPORT CONJOINT ---
        document.getElementById('passeport-conjoint').addEventListener('change', function() {
            const ouiDiv = document.getElementById('passeport-conjoint-oui');
            const nonDiv = document.getElementById('passeport-conjoint-non');
            ouiDiv.classList.add('hidden');
            nonDiv.classList.add('hidden');
            if (this.value === "Oui") ouiDiv.classList.remove('hidden');
            if (this.value === "Non") nonDiv.classList.remove('hidden');
        });

        // --- GESTION COMPAGNIE (Seul, Conjoint, Famille) ---
        const compSelect = document.getElementById('compagnie');
        const secConjoint = document.getElementById('section-conjoint');
        const secEnfants = document.getElementById('section-enfants');

        compSelect.addEventListener('change', function() {
            secConjoint.classList.add('hidden');
            secEnfants.classList.add('hidden');
            
            if (this.value === "Conjoint(e)") {
                secConjoint.classList.remove('hidden');
            } else if (this.value === "Famille") {
                secConjoint.classList.remove('hidden');
                secEnfants.classList.remove('hidden');
                if(document.getElementById('enfants-container').children.length === 0) {
                    ajouterEnfant(); // Ajoute un enfant par défaut
                }
            }
        });

        // --- AJOUT DYNAMIQUE D'ENFANTS ---
        let enfantCount = 0;
        function ajouterEnfant() {
            enfantCount++;
            const container = document.getElementById('enfants-container');
            const html = `
                <div class="child-block" id="enfant-${enfantCount}">
                    <h4>Enfant ${enfantCount}</h4>
                    <div class="form-group" style="margin-top:10px;">
                        <label>Nom Complet</label>
                        <input type="text" name="Enfant ${enfantCount} Nom" placeholder="Ex: Keyna Tayler Lundvall">
                    </div>
                    <div class="form-group">
                        <label>Date de naissance</label>
                        <input type="date" name="Enfant ${enfantCount} DOB">
                    </div>
                    <div class="form-group">
                        <label>Genre</label>
                        <select name="Enfant ${enfantCount} Genre">
                            <option value="">Sélectionnez...</option>
                            <option value="Masculin">Masculin</option>
                            <option value="Féminin">Féminin</option>
                        </select>
                    </div>
                    <div class="form-group">
                        <label>Votre enfant a-t-il un passeport ?</label>
                        <select id="passeport-enfant-${enfantCount}" name="Enfant ${enfantCount} Passeport" onchange="togglePasseportEnfant(${enfantCount})">
                            <option value="">Sélectionnez...</option>
                            <option value="Oui">Oui</option>
                            <option value="Non">Non</option>
                        </select>
                        <div id="passeport-enfant-oui-${enfantCount}" class="hidden" style="margin-top: 10px;">
                            <input type="text" name="Enfant ${enfantCount} Num Passeport" placeholder="Ex: P00034354">
                        </div>
                        <div id="passeport-enfant-non-${enfantCount}" class="warning-msg hidden">⚠️ Merci de passer à nos locaux pour commencer la procédure d’obtention de passeport de votre enfant.</div>
                    </div>
                </div>
            `;
            container.insertAdjacentHTML('beforeend', html);
        }
        
        document.getElementById('btn-add-enfant').addEventListener('click', ajouterEnfant);

        // Rendre global la fonction de toggle pour les enfants dynamiques
        window.togglePasseportEnfant = function(id) {
            const val = document.getElementById(`passeport-enfant-${id}`).value;
            const ouiDiv = document.getElementById(`passeport-enfant-oui-${id}`);
            const nonDiv = document.getElementById(`passeport-enfant-non-${id}`);
            ouiDiv.classList.add('hidden');
            nonDiv.classList.add('hidden');
            if (val === "Oui") ouiDiv.classList.remove('hidden');
            if (val === "Non") nonDiv.classList.remove('hidden');
        };

        // --- VALIDATION ET SOUMISSION VERS XAMPP (AJAX) ---
        const form = document.getElementById('booking-form');
        
        form.addEventListener('submit', async function(e) {
            e.preventDefault(); // Empêche le rechargement brutal
            let isValid = true;
            
            // Réinitialise les erreurs
            document.querySelectorAll('.input-error').forEach(el => el.classList.remove('input-error'));
            document.getElementById('etat-civil-error').style.display = 'none';

            // Vérifie les champs obligatoires (HTML5 validity)
            const requiredElements = form.querySelectorAll('[required]');
            requiredElements.forEach(el => {
                if (!el.value.trim()) {
                    isValid = false;
                    el.classList.add('input-error');
                }
            });

            // Vérif spéciale pour Etat Civil (Radio)
            const radios = document.querySelectorAll('input[name="Etat Civil"]');
            let radioValid = false;
            radios.forEach(r => { if(r.checked) radioValid = true; });
            if(!radioValid) {
                isValid = false;
                document.getElementById('etat-civil-error').style.display = 'block';
            }

            if (!isValid) {
                // Fait défiler vers le premier champ en erreur
                document.querySelector('.input-error, #etat-civil-error').scrollIntoView({ behavior: 'smooth', block: 'center' });
                return; // Stop la soumission
            }

            // Changer le bouton en chargement
            const submitBtn = document.querySelector('.btn-submit');
            const originalText = submitBtn.innerHTML;
            submitBtn.innerHTML = "⏳ Enregistrement en cours...";
            submitBtn.disabled = true;

            // Préparation des données
            const formData = new FormData(form);
            
            try {
                // Modification ici : on envoie à la même page PHP ("") au lieu de Formspree
                const response = await fetch("", {
                    method: 'POST',
                    body: formData
                });

                if (response.ok) {
                    // Cacher le formulaire et afficher le succès
                    form.classList.add('hidden');
                    document.getElementById('success-screen').classList.remove('hidden');
                    window.scrollTo({ top: 0, behavior: 'smooth' });
                } else {
                    alert("Une erreur s'est produite lors de l'enregistrement. Veuillez réessayer.");
                    submitBtn.innerHTML = originalText;
                    submitBtn.disabled = false;
                }
            } catch (error) {
                alert("Erreur de connexion. Vérifiez que votre serveur XAMPP est bien allumé.");
                submitBtn.innerHTML = originalText;
                submitBtn.disabled = false;
            }
        });

        // --- GENERATION DU MESSAGE WHATSAPP ---
        document.getElementById('btn-go-whatsapp').addEventListener('click', function() {
            // Numéro cible
            const phone = "+12063363900";
            
            // Construction du message
            let msg = `*✈️ NOUVELLE RÉSERVATION DE VOYAGE*\n\n`;
            msg += `*👤 INFOS PERSONNELLES*\n`;
            msg += `- Nom: ${document.getElementById('nom').value}\n`;
            msg += `- Téléphone: ${document.getElementById('tel').value}\n`;
            msg += `- Email: ${document.getElementById('email').value}\n`;
            msg += `- Âge: ${document.getElementById('age').value}\n`;
            
            let paysChoisi = document.getElementById('pays').value;
            if(paysChoisi === 'Autre') paysChoisi = document.getElementById('autre-pays').value;
            
            msg += `\n*🌍 DÉTAILS DU VOYAGE*\n`;
            msg += `- Destination: ${paysChoisi}\n`;
            msg += `- Accompagnement: ${document.getElementById('compagnie').value}\n`;
            
            msg += `\nMerci de traiter ma demande !`;

            // Encodage et ouverture
            const url = `https://wa.me/${phone.replace(/[^0-9]/g, '')}?text=${encodeURIComponent(msg)}`;
            window.open(url, '_blank');
        });

        // --- IMPRIMER ---
        document.getElementById('btn-imprimer').addEventListener('click', function() {
            // Fait réapparaître temporairement le formulaire pour l'impression
            document.getElementById('success-screen').style.display = 'none';
            form.classList.remove('hidden');
            window.print();
            // Restaure l'écran après impression
            form.classList.add('hidden');
            document.getElementById('success-screen').style.display = 'block';
        });

    });
</script>

</body>
</html>
