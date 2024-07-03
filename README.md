  #include <DHT.h>

#define DHTPIN 2     // Pino digital onde o DHT11 está conectado
#define DHTTYPE DHT11   // Tipo de sensor DHT
#define SOIL_SENSOR_PIN A0  // Pino analógico onde o sensor de umidade do solo está conectado
#define RELAY_PIN 3   // Pino digital onde o relé está conectado

DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(9600);
  dht.begin();
  pinMode(SOIL_SENSOR_PIN, INPUT);
  pinMode(RELAY_PIN, OUTPUT);
}

void loop() {
  // Lê os dados do DHT11
  float humidity = dht.readHumidity();
  float temperature = dht.readTemperature();

  // Lê os dados do sensor de umidade do solo
  int soilMoistureValue = analogRead(SOIL_SENSOR_PIN);
  
  // Converte o valor lido para umidade percentual (ajuste conforme necessário)
  int soilMoisturePercent = map(soilMoistureValue, 1023, 0, 0, 100);

  Serial.print("Umidade: ");
  Serial.print(humidity);
  Serial.print(" %\t");
  Serial.print("Temperatura: ");
  Serial.print(temperature);
  Serial.println(" *C");

  Serial.print("Umidade do solo: ");
  Serial.print(soilMoisturePercent);
  Serial.println(" %");

  // Define o limiar de umidade do solo para ativar a bomba
  int soilMoistureThreshold = 30;  // Ajuste conforme necessário

  // Se a umidade do solo estiver abaixo do limiar, ativa a bomba
  if (soilMoisturePercent < soilMoistureThreshold) {
    digitalWrite(RELAY_PIN, LOW);  // Relé ativo em nível baixo
    Serial.println("Solo seco - Bomba ativada");
  } else {
    digitalWrite(RELAY_PIN, HIGH); // Relé desativado em nível alto
    Serial.println("Solo úmido - Bomba desativada");
  }

  // Aguarda 2 segundos antes de fazer a próxima leitura
  delay(2000);
}

