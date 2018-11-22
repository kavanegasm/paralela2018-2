# paralela2018-2


Ejercicio 0

 

Esta primera práctica busca evaluar el ambiente de desarrollo que se utilizará en el curso. Debe compilar y probar este ejemplo en el ambiente sin modificar el código.

 

Configuración del proyecto

 

Descomprima el archivo ubicado en http://www.arcesio.net/paralela/ejercicio_0.zip y allí podrá ver el Código Fuente del Proyecto en:

 

ejercicio_0/src/main/java/co/edu/unal/paralela/Setup.java

 

y un archivo para pruebas en:

 

ejercicio_0/src/test/java/co/edu/unal/paralela/SetupTest.java

 

En los ejercicios/practicas del curso se utilizará la herramienta Maven. Maven es una herramienta de construcción (build) que automáticamente gestiona las dependencias, la compilación del código fuente, las pruebas de compilación y de ejecución. Maven se puede utilizar desde la línea de comandos o desde IDEs como Eclipse o IntelliJ.

 

Compilando con Maven desde la línea de comandos

 

Escriba el siguiente comando desde el prompt de commandos dentro de la carpeta ejercicio_0/:

 

$ mvn compile

 

Haciendo pruebas mediante Maven desde la línea de comandos

 

Escriba el siguiente comando desde el prompt de commandos dentro de la carpeta ejercicio_0/:

 

$ mvn test
Ejercicio No. 1

Para el ejercicio_1 se debe utilizar el framework Fork Join de java para escribir una implementación paralela de la suma de los recíprocos de cada elemento de un arreglo.

El cálculo de la suma de los recíprocos de un arreglo implica sumar todos los elementos del arreglo. El recíproco de un valor v es 1/v.

Es importante observar que el cálculo del recíproco en cada paso de la iteración es independiente del cálculo del recíproco de cualesquier otra iteración, de tal forma que el problema puede paralelizarse. En particular el grafo computacional puede ser visualizado

Después de descargar el Proyecto de http://www.arcesio.net/paralela/ejercicio_1.zip, debe revisar el código fuente de

Ejercicio_1/src/main/java/co/edu/unal/paralela/𝚁𝚎𝚌𝚒𝚙𝚛𝚘𝚌𝚊𝚕𝙰𝚛𝚛𝚊𝚢𝚂𝚞𝚖.𝚓𝚊v𝚊

Y la prueba del Proyecto

Ejercicio_1/src/main/java/co/edu/unal/paralela/ 𝚁𝚎𝚌𝚒𝚙𝚛𝚘𝚌𝚊𝚕𝙰𝚛𝚛𝚊𝚢𝚂𝚞𝚖𝚃𝚎𝚜𝚝.𝚓𝚊𝚟𝚊

Instrucciones

Las modificaciones deben realizarse únicamente dentro del archive ReciprocalArraySum.java. NO se deben cambiar las ‘signatures’ de los métodos publicos (public) ni protegidos (protected) dentro de ReciprocalArraySum, pero se pueden editar el cuerpo de los métodosy adicional nuevos métodos si lo considera.

Actividades a realizar:

1. Modificar el método ReciprocalArraySum.parArraySum() para implementar el cálculo de la suma de los recíprocos de un arreglo utilizando el framework Fork Join de Java mediante la partición del arreglo de entrada a la mitad y calculando la suma de los recíprocos del arreglo a partir de la suma de la primera parte y de la segunda parte en paralelo, antes de combinar los resultados. Hay varias actividades “Para hacer” en el código fuente como guía.

2. Modificar el método ReciprocalArraySum.parManyTaskArraySum para implementar el cálculo de la suma de los recíprocos de un arreglo en paralelo utilizando el framework Fork Join de Java nuevamente, pero utilizando un número de tareas estipulado (no solo 2 tareas como en la actividad 1). Observe que los métodos getChunkStartInclusive y getChunkEndExclusive se presentan para ayudar a calcular la región del arreglo de entrada que cierta tarea debe procesar.

 

Ejemplo código videos

 

public static double seqArraySum(double[] X) {

       long startTime = System.nanoTime();

       double sum = 0;

       for (int i=0; i < X.length; i++) {

             sum += 1/X[i];

       }

       long timeInNanos = System.nanoTime() - startTime;

       printResults("seqArraySum", timeInNanos, sum);

       // Tarea T0 espera por la tarea T1 (join)

       return sum;

}

 

/**

 * <p>parArraySum.</p>

 *

 * @param X una arreglo tipo double

 * @return sum of 1/X[i] for 0 <= i < X.length

*/

public static double parArraySum(double[] X) {

       long startTime = System.nanoTime();

       SumArray t = new SumArray(X, 0, X.length);

       ForkJoinPool.commonPool().invoke(t);

       double sum = t.ans;

       long timeInNanos = System.nanoTime() - startTime;

       printresults("parArraySum", timeInNanos, sum);

       return sum;

}

 

/**

 * <p>pmain</p>

 *

 * @param argv an array of double

 */

public static void main(final String[] argv) {

       // Initialization

       int n;

       if(argv.length !=0) {

             try {

                    n = integer.parseInt(argv[0]);

                    if(n <=0 ) {

                           // valor incorrecto de n

                           system.out.println(ERROR_MSG);

                           n = DEFAULT_N;

                    }

             } catch (Throwable e) {

                    System.out.println(ERROR_MSG);

                    n = DEFAULT_N;

             }

       } else { // argv.length == 0

             n = DEFAULT_N;

       }

       double[] X = new double[n];

      

       for(int i=0; i <n; i++) {

             X[i] = (i + 1);

       }

      

       // establece el número de 'workers' utilizados por ForKJoinPool.commonPool()

       System.setProperty("java.util.concurrent.ForkJoinPool.common.parallelism", "2");

      

       for (int numRun = 0; numRun < 5; numRun++) {

             System.out.printf("Run %d\n",numRun);

             seqArraySum(X);

             parArraySum(X);

       }

}

      

private static void printResults (String name, long timeInNanos, double sum) {

       system.out.printf(" %s completed in %8.3f milliseconds, with sum = %8.5f \n", name, timeInNanos / 1e6, sum);

}

 

private static class SumArray extends recursiveAction {

       static int SEQUENTIAL_THRESHOLD = 5;

       int lo;

       int hi;

       double arr[];

       double ans = 0;

      

       SumArray(double[] a, int l, int h) {

             lo =l;

             hi = h;

             arr = a;

       }

      

       protected void compute() {

             if (hi - lo <= SEQUENTIAL_THRESHOLD) {

                    for (int = lo; i < hi; ++i)

                           ans += 1 / arr[i];

             } else {

                    SumArray left = new SumArray(arr, lo, (hi + lo) /2);

                    SumArray right = new SumArray(arr, lo, (hi + lo) /2);

                    left.fork;

                    right.compute();

                    left.join();

                    ans = left.ans + right.ans;

             }

       }

}
Ejercicio_2
Descargar el archivo www.arcesio.net/paralela/ejercicio_2.zip
Las modificaciones deben ser realizadas dentro de StudentAnalytics.java. 
No se puede modificar las firmas (signatures) de los métodos públicos ni protegidos dentro de StudentAnalytics ni borrarlos. Puede adicionar los métodos que desee. 
Implementar StudentAnalytics.averageAgeOfEnrolledStudentsParallelStream para que haga lo mismo que averageAgeOfEnrolledStudentsImperative pero usando streams paralelos.
Implementar StudentAnalytics. mostCommonFirstNameOfInactiveStudentsParallelStream para que haga lo mismo que mostCommonFirstNameOfInactiveStudentsImperative usando streams paralelos.
Implementar StudentAnalytics. countNumberOfFailedStudentsOlderThan20ParallelStream para que haga lo mismo que countNumberOfFailedStudentsOlderThan20Imperative usando streams paralelos. 
https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html

Paralelismo en ciclos
http://www.arcesio.net/paralela/ejercicio_3.zip
El objetivo de este ejercicio es implementar paralelismo en ciclos utilizando pcdp. El código entregado incluye un programa secuencial para multiplicar dos matrices. El resultado debe ser el código para ejecución en paralelo que se ejecute más rápido que la versión secuencial. 
Las modificaciones solo se deben realizar en MatrixMultiply.java. No se pueden hacer cambios en las firmas (signatures) de los métodos públicos (public) ni en los protegidos (protected)  dentro de MatrixMultiply, ni se pueden borrar. Se pueden adicionar métodos nuevos si se desea 
MatrixMultiply.java contiene varios PARA HACER:
Modificar el método MatrixMultiply.parMatrixMultiply para implementar la multiplicación de matrices en paralelo utilizando los métodos forall o forallChunked de pcdp. Hay un PARA HACER en MatrixMultiply.parMatrixMultiply para orientar los cambios que deben ser realizados. Una implementación paralela de MatrixMultiply.parMatrixMultiply debe mejorar la rapidez de forma casi lineal (i.e. la mejora en la rapidez lograda debe ser muy cercana al número de cores del computador).

Código ejemplo en las siguientes dos páginas



public static void seqMatrixMultiply(double[][] A, double B[][], double[][] C, int n) throws SuspendableException {
	long startTime = System.nanoTime();
	forseq(0, n-1, 0, n-1, (i,j) ->
		{
			C[i][j] = 0;
			for (int k =0; k < n; k++) {
				C[i][j] += A[i][k] * B[k][j];
			}
		}
	);
	long timeInNanos = System.nanoTime() - startTime;
	printResults("seqMatrixMultiply", timeInNanos, C[n-1][n-1]);
}

public static void parMatrixMultiply(double[][] A, double B[][], double[][] C, int n) throws SuspendableException {
	long startTime = System.nanoTime();
	forall(0, n-1, 0, n-1, (i,j) ->
		{
			C[i][j] = 0;
			for (int k =0; k < n; k++) {
				C[i][j] += A[i][k] * B[k][j];
			}
		}
	);
	long timeInNanos = System.nanoTime() - startTime;
	printResults("seqMatrixMultiply", timeInNanos, C[n-1][n-1]);
}


public static void parMatrixMultiplyChunked(double[][] A, double B[][], double[][] C, int n) throws SuspendableException {
	long startTime = System.nanoTime();
	forallChunked(0, n-1, 0, n-1, (i,j) ->
		{
			C[i][j] = 0;
			for (int k =0; k < n; k++) {
				C[i][j] += A[i][k] * B[k][j];
			}
		}
	);
	long timeInNanos = System.nanoTime() - startTime;
	printResults("seqMatrixMultiply", timeInNanos, C[n-1][n-1]);
}

private static void printResults(string name, long timeInNanos, double sum) {
	System.out.printf(" %s completed in %8.3f milliseconds, with C[n-1][n-1] = %8.5f \n", name, timeInNanos / 1e6, sum);
}

Sincronización de flujo de datos y Pipelining 

http://www.arcesio.net/paralela/ejercicio_4.zip
Aplicar phasers de java e hilos de java a un algoritmo de promedio iterativo uni-dimensional. 
Las modificaciones solo se deben realizar en OneDimAveragingPhaser.java. No se pueden hacer cambios en las firmas (signatures) de los métodos públicos (public) ni en los protegidos (protected)  dentro de OneDimAveragingPhaser, ni se pueden borrar. Se pueden adicionar métodos nuevos si se desea 
OneDimAveragingPhaser.java contiene un PARA HACER:
Basado en la version secuencial proporcionada en OneDimAveragingPhaser.runSequential y la version paralela en OneDimAveragingPhaser.runParallelBarrier, implementar una version paralela que utilice phasers para maximizar la superposición entre la terminación de  la barrera y la realización de trabajo útil.



public static void seqMatrixMultiply(double[][] A, double B[][], double[][] C, int n) throws SuspendableException {
	long startTime = System.nanoTime();
	forseq(0, n-1, 0, n-1, (i,j) ->
		{
			C[i][j] = 0;
			for (int k =0; k < n; k++) {
				C[i][j] += A[i][k] * B[k][j];
			}
		}
	);
	long timeInNanos = System.nanoTime() - startTime;
	printResults("seqMatrixMultiply", timeInNanos, C[n-1][n-1]);
}

public static void parMatrixMultiply(double[][] A, double B[][], double[][] C, int n) throws SuspendableException {
	long startTime = System.nanoTime();
	forall(0, n-1, 0, n-1, (i,j) ->
		{
			C[i][j] = 0;
			for (int k =0; k < n; k++) {
				C[i][j] += A[i][k] * B[k][j];
			}
		}
	);
	long timeInNanos = System.nanoTime() - startTime;
	printResults("seqMatrixMultiply", timeInNanos, C[n-1][n-1]);
}


public static void parMatrixMultiplyChunked(double[][] A, double B[][], double[][] C, int n) throws SuspendableException {
	long startTime = System.nanoTime();
	forallChunked(0, n-1, 0, n-1, (i,j) ->
		{
			C[i][j] = 0;
			for (int k =0; k < n; k++) {
				C[i][j] += A[i][k] * B[k][j];
			}
		}
	);
	long timeInNanos = System.nanoTime() - startTime;
	printResults("seqMatrixMultiply", timeInNanos, C[n-1][n-1]);
}

private static void printResults(string name, long timeInNanos, double sum) {
	System.out.printf(" %s completed in %8.3f milliseconds, with C[n-1][n-1] = %8.5f \n", name, timeInNanos / 1e6, sum);
}
