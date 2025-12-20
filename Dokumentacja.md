# Część I - CI/CD Pipeline
.github\workflows\cicd.yml

Korzystając z GH actions został stworzony pipeline, który wykonuje następujące zadania:
1. **Tworzy środowisko**
 - Ubuntu
 - Python
 - ustawia zmienne środowiskowe z secretów
 - instaluje zależności
 ```yaml
     - name: Install backend dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r back-end/requirements.txt
          pip install -r back-end/requirements_test.txt
      - name: Install frontend dependencies
        run: |
          pip install -r front-end/requirements.txt
          pip install -r front-end/requirements_test.txt

 ```
2. **Przeprowadza analizę kodu przy pomocy CodeQL**


```yaml
      - name: Initialize CodeQL
        uses: github/codeql-action/init@v4
        with:
          languages: python
      - name: CodeQL Autobuild
        uses: github/codeql-action/autobuild@v4
      - name: CodeQL Analysis
        uses: github/codeql-action/analyze@v4
```
3. **Wykonuje linting kodu przy pomocy Ruff**
```yaml
      - name: Install code linting tool
        run: pip install ruff
      - name: Lint backend code
        run: ruff check --output-format=github back-end/
        continue-on-error: true
      - name: Lint frontend code
        run: ruff check --output-format=github front-end/
        continue-on-error: true
```
4. **Przeprowadza testy aplikacji**
```yaml
      - name: Run backend tests
        run: pytest back-end/test_app.py
        continue-on-error: true
      - name: Run frontend tests
        run: pytest front-end/test_app.py
        continue-on-error: true
```
5. **Wrzuca kontenery na GHRC**

Proces ten zawiera w sobie:
- logowanie
- ekstrakcję metadanych
- budowanie i wgrywanie obrazu

# Część II - Helm chart for the project
.wuda

W ramach tej części został stworzony pełny proces wdrożenia aplikacji na kubernetesa. 

1. Konfiguracja Helm-a - wuda\values.yaml

2. Secrety - wuda\templates\secrets.yaml

3. Deployment bazy danych - wuda\templates\deployDatabase.yaml

4. Service bazy danych - wuda\templates\serviceDatabase.yaml

5. Deployment backendu - wuda\templates\deployBackend.yaml 
 - posiada on initialcontainer zrobiony przy pomocy busybox w celu oczekiwania na uruchomienia bazy danych oraz kontener dla backendu
6. Service becendu - wuda\templates\serviceBackend.yaml
7. Deployment Frontendu - wuda\templates\deployFrontend.yaml
8. Service Frontendu - wuda\templates\serviceFrontend.yaml
9. Ingress - wuda\templates\ingress.yaml
- Pozwala na dostęp do clastra z zewnątrz
10. Konfiguracja kinda - wuda\kind-config.yaml