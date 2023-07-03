# AlvaroLab
public class EditorialXMLDAO {

    private Document document;
    private Element raiz;
    private String rutaDocumento;

    public EditorialXMLDAO(String rutaDocumento, String nombreRaiz) throws IOException {
        this.raiz = new Element(nombreRaiz);
        this.document = new Document(raiz);
        this.rutaDocumento = rutaDocumento;
        guardar();
    }

    public static EditorialXMLDAO crearDocumento(String rutaDocumento) throws IOException {
        return new EditorialXMLDAO(rutaDocumento, "editoriales");
    }

    private EditorialXMLDAO(String rutaDocumento) throws IOException, JDOMException {
        SAXBuilder saxBuilder = new SAXBuilder();
        saxBuilder.setIgnoringElementContentWhitespace(true);
        this.document = saxBuilder.build(rutaDocumento);
        this.raiz = document.getRootElement();
        this.rutaDocumento = rutaDocumento;
        guardar();
    }

    public static EditorialXMLDAO abrirDocumento(String rutaDocumento) throws IOException, JDOMException {
        return new EditorialXMLDAO(rutaDocumento);
    }

    public void guardar() throws IOException {
        XMLOutputter xmlOutputter = new XMLOutputter();
        xmlOutputter.setFormat(Format.getPrettyFormat());
        xmlOutputter.output(this.document, new FileWriter(this.rutaDocumento));
        xmlOutputter.output(this.document, System.out); // Extra para revisión
    }

    public void insertarEditorial(Editorial editorial) throws IOException {
        Element eEditorial = new Element("editorial");
        eEditorial.setAttribute("idEditorial", String.valueOf(editorial.getIdEditorial()));
        eEditorial.addContent(new Element("nombreEditorial").setText(editorial.getNombreEditorial()));
        eEditorial.addContent(new Element("ubicacion").setText(editorial.getUbicacion()));

        raiz.addContent(eEditorial);
        guardar();
    }

    public void modificarEditorial(Editorial editorialModificado) throws IOException {
        String idEditorial = editorialModificado.getIdEditorial();

        Element elemento = raiz.getChild("editorial");

        if (elemento.getAttributeValue("idEditorial").equals(String.valueOf(idEditorial))) {
            elemento.getChild("nombreEditorial").setText(editorialModificado.getNombreEditorial());
            elemento.getChild("ubicacion").setText(editorialModificado.getUbicacion());
        }

        guardar();
    }


//    public ArrayList<Editorial> getEditoriales() throws DataConversionException {
//        List eListaEditorial = raiz.getChildren();
//        ArrayList<Editorial> editoriales = new ArrayList<Editorial>();
//
//        for (Object obj: eListaEditorial) {
//
//            Element eEditorial = (Element)obj;
//            Editorial editorialActual = new Editorial();
//            //child --> ingresar al objeto
//            editorialActual.setIdEditorial(String.valueOf(eEditorial.getAttribute("idEditorial").getIntValue()));
//            editorialActual.setNombreEditorial(String.valueOf(eEditorial.getAttribute("nombreEditorial")));
//            editorialActual.setUbicacion(String.valueOf(eEditorial.getAttribute("ubicacion")));
//            editoriales.add(editorialActual);
//        }
//        return editoriales;
//    }
    public List<Editorial> getEditoriales() throws DataConversionException {
        List<Element> elementos = raiz.getChildren("editorial");
        List<Editorial> editoriales = new ArrayList<>();

        for (Element obj : elementos) {
            Element eEditorial = obj;
            Editorial editorialActual = new Editorial();

            //child --> ingresar al objeto
            editorialActual.setIdEditorial(String.valueOf(eEditorial.getAttribute("idEditorial").getIntValue()));
            editorialActual.setNombreEditorial(eEditorial.getChildText("nombreEditorial"));
            editorialActual.setUbicacion(eEditorial.getChildText("ubicacion"));
            editoriales.add(editorialActual);
            editoriales.add(editorialActual);
        }
        return editoriales;
    }


public void borrarEditorial(int idEditorial) throws IOException {
        Element elemento = buscarElementoEditorial(idEditorial);

        if (elemento != null) {
            raiz.removeContent(elemento);
            guardar();
        }
    }

    private Element buscarElementoEditorial(int idEditorial) {
        List<Element> elementos = raiz.getChildren("editorial");

        for (Element elemento : elementos) {
            int id = Integer.parseInt(elemento.getAttributeValue("idEditorial"));
            if (id == idEditorial) {
                return elemento;
            }
        }

        return null;
    }
}








package data;

import models.Libro;
import org.jdom2.DataConversionException;
import org.jdom2.Document;
import org.jdom2.Element;
import org.jdom2.JDOMException;
import org.jdom2.input.SAXBuilder;
import org.jdom2.output.Format;
import org.jdom2.output.XMLOutputter;

import java.io.FileWriter;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

public class CatalogoLibrosXML {
    private Document document;
    private Element raiz;
    private String rutaDocumento;

    public CatalogoLibrosXML(String rutaDocumento, String nombreRaiz) throws IOException {
        this.raiz = new Element(nombreRaiz);
        this.document = new Document(raiz);
        this.rutaDocumento = rutaDocumento;
        guardar();
    }

    public static CatalogoLibrosXML crearDocumento(String rutaDocumento) throws IOException {
        return new CatalogoLibrosXML(rutaDocumento, "catalogoLibros");
    }

    private CatalogoLibrosXML(String rutaDocumento) throws IOException, JDOMException {
        SAXBuilder saxBuilder = new SAXBuilder();
        saxBuilder.setIgnoringElementContentWhitespace(true);
        this.document = saxBuilder.build(rutaDocumento);
        this.raiz = document.getRootElement();
        this.rutaDocumento = rutaDocumento;
        guardar();
    }

    public static CatalogoLibrosXML abrirDocumento(String rutaDocumento) throws IOException, JDOMException {
        return new CatalogoLibrosXML(rutaDocumento);
    }

    public void guardar() throws IOException {
        XMLOutputter xmlOutputter = new XMLOutputter();
        xmlOutputter.setFormat(Format.getPrettyFormat());
        xmlOutputter.output(this.document, new FileWriter(this.rutaDocumento));
        xmlOutputter.output(this.document, System.out); // Extra para revisión
    }

    public void insertarLibro(Libro libro) throws IOException {
        Element eLibro = new Element("libro");
        eLibro.setAttribute("libroID", String.valueOf(libro.getLibroID()));
        eLibro.addContent(new Element("nombreLibro").setText(libro.getNombreLibro()));
        eLibro.addContent(new Element("editorial").setText(libro.getEditorial()));
        eLibro.addContent(new Element("fechaPublicacion").setText(libro.getFechaPublicacion()));
        eLibro.addContent(new Element("tematica").setText(libro.getTematica()));
        eLibro.addContent(new Element("isbn").setText(libro.getIsbn()));

        // Concatenar los IDs de los autores separados por comas
        String idsAutores = String.join(",", (CharSequence) libro.getIdsAutores());
        eLibro.addContent(new Element("idAutores").setText(idsAutores));

        raiz.addContent(eLibro);
        guardar();
    }


    public ArrayList<Libro> getLibros() throws DataConversionException {
        List<Element> elementos = raiz.getChildren("libro");
        ArrayList<Libro> libros = new ArrayList<>();

        for (Element elemento : elementos) {
            Libro libro = crearLibroDesdeElemento(elemento);
            libros.add(libro);
        }

        return libros;
    }

    private Libro crearLibroDesdeElemento(Element elemento) {
        Libro libro = new Libro();
        libro.setLibroID(elemento.getAttributeValue("libroID"));
        libro.setNombreLibro(elemento.getChildText("nombreLibro"));
        libro.setEditorial(elemento.getChildText("editorial"));
        libro.setFechaPublicacion(elemento.getChildText("fechaPublicacion"));
        libro.setTematica(elemento.getChildText("tematica"));
        libro.setIsbn(elemento.getChildText("isbn"));

        return libro;
    }

    public List<Libro> buscarLibrosPorTitulo(String titulo) throws DataConversionException {
        return buscarLibrosPorCriterio("titulo", titulo);
    }

    public List<Libro> buscarLibrosPorAutor(String autor) throws DataConversionException {
        return buscarLibrosPorCriterio("autor", autor);
    }

    public List<Libro> buscarLibrosPorTema(String tema) throws DataConversionException {
        return buscarLibrosPorCriterio("tema", tema);
    }

    public List<Libro> buscarLibrosPorEditorial(String editorial) throws DataConversionException {
        return buscarLibrosPorCriterio("editorial", editorial);
    }

    private List<Libro> buscarLibrosPorCriterio(String criterio, String valor) throws DataConversionException {
        List<Element> elementos = raiz.getChildren("libro");
        List<Libro> librosEncontrados = new ArrayList<>();

        for (Element elemento : elementos) {
            String valorCriterio = elemento.getChildText(criterio);
            if (valorCriterio != null && valorCriterio.equals(valor)) {
                Libro libro = crearLibroDesdeElemento(elemento);
                librosEncontrados.add(libro);
            }
        }

        return librosEncontrados;
    }

    public void modificarLibro(Libro libroModificado) throws IOException {
        int libroID = Integer.parseInt(libroModificado.getLibroID());

        Element elemento = raiz.getChild("libro");

        if (elemento.getAttributeValue("libroID").equals(String.valueOf(libroID))) {
            elemento.getChild("nombreLibro").setText(libroModificado.getNombreLibro());
            elemento.getChild("editorial").setText(libroModificado.getEditorial());
            elemento.getChild("fechaPublicacion").setText(libroModificado.getFechaPublicacion());
            elemento.getChild("tematica").setText(libroModificado.getTematica());
            elemento.getChild("isbn").setText(libroModificado.getIsbn());

            // Eliminar los IDs de los autores anteriores
            List<Element> elementosAutorId = elemento.getChildren("idAutor");
            elementosAutorId.forEach(elemento::removeContent);

            // Agregar los nuevos IDs de los autores
            /*for (int autorId : libroModificado.getIdsAutores()) {
                Element eAutorId = new Element("idAutor").setText(String.valueOf(autorId));
                elemento.addContent(eAutorId);
            }*/

            guardar();
        }
    }

public void borrarLibro ( int libroID) throws IOException {
            Element elemento = buscarElementoLibro(libroID);

            if (elemento != null) {
                raiz.removeContent(elemento);
                guardar();
            }
        }

        private Element buscarElementoLibro ( int libroID){
            List<Element> elementos = raiz.getChildren("libro");

            for (Element elemento : elementos) {
                int id = Integer.parseInt(elemento.getAttributeValue("libroID"));
                if (id == libroID) {
                    return elemento;
                }
            }

            return null;
        }
}























public List<Editorial> getEditoriales() throws DataConversionException {
    List<Element> elementos = raiz.getChildren("editorial");
    List<Editorial> editoriales = new ArrayList<>();

    for (Element obj : elementos) {
        Element eEditorial = obj;
        Editorial editorialActual = new Editorial();

        editorialActual.setIdEditorial(String.valueOf(eEditorial.getAttribute("idEditorial").getIntValue()));
        editorialActual.setNombreEditorial(eEditorial.getChildText("nombreEditorial"));
        editorialActual.setUbicacion(eEditorial.getChildText("ubicacion"));

        // Obtener los elementos de "Tema" dentro de la editorial
        List<Element> temas = eEditorial.getChildren("Tema");
        List<Tema> temasList = new ArrayList<>();

        for (Element tema : temas) {
            Tema temaActual = new Tema();
            temaActual.setIdTema(String.valueOf(tema.getAttribute("idTema").getIntValue()));
            temaActual.setNombreTema(tema.getChildText("nombreTema"));

            // Obtener los elementos hijos del "Tema"
            List<Element> hijos = tema.getChildren();

            for (Element hijo : hijos) {
                // Extraer información adicional de los hijos del "Tema" si es necesario
                if (hijo.getName().equals("elemento1")) {
                    // Procesar "elemento1"
                } else if (hijo.getName().equals("elemento2")) {
                    // Procesar "elemento2"
                }
                // ... Continuar con los demás elementos que necesites extraer dentro de "Tema"
            }

            temasList.add(temaActual);
        }

        editorialActual.setTemas(temasList);
        editoriales.add(editorialActual);
    }

    return editoriales;
}
