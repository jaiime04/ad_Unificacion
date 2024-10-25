/*
 * Click nbfs://nbhost/SystemFileSystem/Templates/Licenses/license-default.txt to change this license
 * Click nbfs://nbhost/SystemFileSystem/Templates/Classes/Class.java to edit this template
 */
package com.mycompany.unificacion;

import java.io.FileWriter;
import java.io.IOException;
import java.sql.*;

/**
 *
 * @author merin
 */
public class Conexion {
     
    
    private static final String URL = "jdbc:mariadb://localhost:3306/nation";
    private static final String USER = "root";
    private static final String PASSWORD = "";
    private Connection connection;

    // Crear conexión
    public Connection conectar() throws SQLException {        
        connection = DriverManager.getConnection(URL, USER, PASSWORD);
        return connection;
    }

    // Cerrar conexión
    public void cerrar() throws SQLException {
        if (connection != null && !connection.isClosed()) {
            connection.close();
        }
    }
    //listar lenguajePaisesAfricanos
    public static void listarLenguajesDePaisesAfricanos() {
    String query = "SELECT c.name AS country, l.language AS language " +
                   "FROM countries c " +
                   "JOIN country_languages cl ON c.country_id = cl.country_id " +
                   "JOIN languages l ON cl.language_id = l.language_id " +
                   "JOIN regions r ON c.region_id = r.region_id " +
                   "JOIN continents co ON r.continent_id = co.continent_id " +
                   "WHERE co.name = 'Africa';";

    try (Connection conn = new Conexion().conectar(); // Usa el método conectar
         PreparedStatement ps = conn.prepareStatement(query);
         ResultSet rs = ps.executeQuery()) {

        // Recorrer y mostrar los resultados
        System.out.println("País -> Lenguaje");
        while (rs.next()) {
            String pais = rs.getString("country");
            String lenguaje = rs.getString("language");
            System.out.println(pais + " -> " + lenguaje);
        }

    } catch (SQLException e) {
        e.printStackTrace(); // Manejo de excepciones específico
    }
    }
    
    //insertar esperanto a lenguajes
    public static void insertEsperantoInUE() {
    String query = "SELECT language_id FROM languages WHERE language = 'Esperanto'";
    int esperantoId = -1;
    try (Connection conn = new Conexion().conectar();
         PreparedStatement ps = conn.prepareStatement(query);
         ResultSet rs = ps.executeQuery()) {

        // Verificar si Esperanto ya existe en la tabla de idiomas
        if (rs.next()) {
            esperantoId = rs.getInt("language_id");
            System.out.println("El idioma Esperanto ya existe con ID: " + esperantoId);
            return; // Detenemos la ejecución si Esperanto ya existe
        } else {
            // Si no existe, insertamos el idioma Esperanto
            String insertEsperantoQuery = "INSERT INTO languages (language) VALUES ('Esperanto')";
            try (PreparedStatement insertPs = conn.prepareStatement(insertEsperantoQuery, PreparedStatement.RETURN_GENERATED_KEYS)) {
                insertPs.executeUpdate();
                try (ResultSet generatedKeys = insertPs.getGeneratedKeys()) {
                    if (generatedKeys.next()) {
                        esperantoId = generatedKeys.getInt(1);
                    }
                }
            }
        }

        // Obtener los países de la UE
        String getCountriesQuery = "SELECT country_id FROM countries WHERE region_id IN (SELECT region_id FROM regions WHERE name = 'Europe')";
        try (PreparedStatement getCountriesPs = conn.prepareStatement(getCountriesQuery);
             ResultSet countriesRs = getCountriesPs.executeQuery()) {

            // Insertar Esperanto como idioma para cada país de la UE
            String insertCountryLanguageQuery = "INSERT INTO country_languages (country_id, language_id, official) VALUES (?, ?, ?)";
            try (PreparedStatement insertCountryLanguagePs = conn.prepareStatement(insertCountryLanguageQuery)) {
                while (countriesRs.next()) {
                    int countryId = countriesRs.getInt("country_id");
                    insertCountryLanguagePs.setInt(1, countryId);
                    insertCountryLanguagePs.setInt(2, esperantoId);
                    insertCountryLanguagePs.setBoolean(3, false); // Suponemos que no es oficial
                    insertCountryLanguagePs.executeUpdate();
                }
            }
        }
        System.out.println("Esperanto ha sido insertado en todos los países de la UE.");
        
    } catch (SQLException e) {
        e.printStackTrace(); // Manejo de excepciones
    }
    }
    
    //
    public static void escribirRegionFichero(String regionName, String fileName) throws IOException {
    String queryId = "SELECT region_id FROM regions WHERE region_id = ?";
    String query = "SELECT c.name AS country_id, l.language AS language_id " +
                   "FROM countries c " +
                   "JOIN regions r ON c.region_id = r.region_id " +
                   "JOIN country_languages cl ON c.country_id = cl.country_id " +
                   "JOIN languages l ON cl.language_id = l.language_id " +
                   "WHERE r.region_id = ?";

    try (Connection conn = new Conexion().conectar();
         PreparedStatement psId = conn.prepareStatement(queryId);
         PreparedStatement ps = conn.prepareStatement(query);
         FileWriter fileWriter = new FileWriter(fileName)) {

        // Obtener el region_id
        psId.setString(1, regionName);
        ResultSet resultSetId = psId.executeQuery();

        if (resultSetId.next()) {
            String regionId = resultSetId.getString("region_id");

            // Establecer el parámetro de la consulta
            ps.setString(1, regionId);

            ResultSet resultSet = ps.executeQuery();

            // Escribir el resultado en el archivo de texto
            while (resultSet.next()) {
                fileWriter.write(resultSet.getString("country_id") + " " + resultSet.getString("language_id") + System.lineSeparator());
            }

            System.out.println("Query results written to " + fileName);
        } else {
            System.err.println("No region found with name: " + regionName);
        }

    } catch (SQLException e) {
        System.err.println("SQL Error: " + e.getMessage());
    } catch (IOException e) {
        System.err.println("IO Error: " + e.getMessage());
    }
}



}
