import com.intellij.database.model.DasTable
import com.intellij.database.util.Case
import com.intellij.database.util.DasUtil

/*
 * Available context bindings:
 *   SELECTION   Iterable<DasObject>
 *   PROJECT     project
 *   FILES       files helper
 */

packageName = "com.sample;"
typeMapping = [
        (~/(?)bigint/)                    : "Long",
        (~/(?i)int/)                      : "Integer",
        (~/(?i)float|double|decimal|real/): "BigDecimal",
        (~/(?i)datetime|timestamp/)       : "Date",
        (~/(?i)date/)                     : "Date",
        (~/(?i)time/)                     : "Date",
        (~/(?i)/)                         : "String"
]

FILES.chooseDirectoryAndSave("Choose directory", "Choose where to store generated files") { dir ->
    SELECTION.filter { it instanceof DasTable }.each { generate(it, dir) }
}

def generate(table, dir) {
    def className = javaName(table.getName(), true)
    def fields = calcFields(table)
    def file  = new File(dir, className + ".java")
    def printWriter = new PrintWriter(new OutputStreamWriter(new FileOutputStream(file),"utf-8"))
    printWriter.withPrintWriter { out -> generate(table,out, className, fields) }
}

def generate(table, out, className, fields) {

    out.println "package $packageName"
    out.println ""
    out.println "import lombok.Data;"
    out.println "import java.io.Serializable;"
    out.println "import java.util.Date;"
    out.println "import lombok.Data;"
    out.println "import com.baomidou.mybatisplus.annotation.IdType;"
    out.println "import com.baomidou.mybatisplus.annotation.TableId;"
    out.println "import com.baomidou.mybatisplus.annotation.TableName;"

    out.println ""
    out.println "@Data"
    out.println "@TableName(\"" + table.getName() +"\")"
    out.println "public class $className implements Serializable {"
    out.println ""
    fields.each() {
        if (it.annos != "") out.println "  ${it.annos}"
        out.println "  /** ${it.comment} **/"
        if (it.name == "id") out.println "  @TableId(type=IdType.AUTO)"
        out.println "  private ${it.type} ${it.name};"
    }
    out.println ""
//    fields.each() {
//        out.println ""
//        out.println "  public ${it.type} get${it.name.capitalize()}() {"
//        out.println "    return ${it.name};"
//        out.println "  }"
//        out.println ""
//        out.println "  public void set${it.name.capitalize()}(${it.type} ${it.name}) {"
//        out.println "    this.${it.name} = ${it.name};"
//        out.println "  }"
//        out.println ""
//    }
    out.println "}"
}

def calcFields(table) {
    DasUtil.getColumns(table).reduce([]) { fields, col ->
        def spec = Case.LOWER.apply(col.getDasType().getSpecification())
        def typeStr = typeMapping.find { p, t -> p.matcher(spec).find() }.value
        fields += [[
                           name : javaName(col.getName(), false),
                           type : typeStr,
                           annos: "",
                           comment: col.getComment()
                   ]]
    }
}

def javaName(str, capitalize) {
    def s = com.intellij.psi.codeStyle.NameUtil.splitNameIntoWords(str)
            .collect { Case.LOWER.apply(it).capitalize() }
            .join("")
            .replaceAll(/[^\p{javaJavaIdentifierPart}[_]]/, "_")
    capitalize || s.length() == 1 ? s : Case.LOWER.apply(s[0]) + s[1..-1]
}
