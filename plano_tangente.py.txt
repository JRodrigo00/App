import streamlit as st
import numpy as np
import sympy as sp
import matplotlib.pyplot as plt

st.title("Plano Tangente Interactivo")

# Entrada de la función
func_str = st.text_input("Ingresa la función f(x, y):", "x**2 + y**2")
# Entrada del punto (a,b)
a = st.number_input("Coordenada a (x):", value=1.0)
b = st.number_input("Coordenada b (y):", value=2.0)

if st.button("Calcular"):
    x, y = sp.symbols('x y')
    try:
        # Convertir cadena a expresión simbólica
        f = sp.sympify(func_str)

        # Derivadas parciales
        fx = sp.diff(f, x)
        fy = sp.diff(f, y)

        # Evaluar en el punto (a,b)
        f0 = float(f.subs({x: a, y: b}))
        fx0 = float(fx.subs({x: a, y: b}))
        fy0 = float(fy.subs({x: a, y: b}))

        # Mostrar ecuación del plano tangente
        st.latex(r"z = %.4f + %.4f (x - %.4f) + %.4f (y - %.4f)" % (f0, fx0, a, fy0, b))

        # Crear malla para graficar
        Xg = np.linspace(a - 2, a + 2, 50)
        Yg = np.linspace(b - 2, b + 2, 50)
        Xg, Yg = np.meshgrid(Xg, Yg)

        # Función original evaluada
        f_lambdified = sp.lambdify((x, y), f, 'numpy')
        Zg = f_lambdified(Xg, Yg)

        # Plano tangente
        Zp = f0 + fx0 * (Xg - a) + fy0 * (Yg - b)

        # Graficar superficie y plano tangente
        fig = plt.figure(figsize=(8,6))
        ax = fig.add_subplot(111, projection='3d')
        ax.plot_surface(Xg, Yg, Zg, alpha=0.6, cmap='viridis')
        ax.plot_surface(Xg, Yg, Zp, alpha=0.5, cmap='coolwarm')
        ax.scatter(a, b, f0, color='red', s=50)

        ax.set_xlabel('x')
        ax.set_ylabel('y')
        ax.set_zlabel('z')
        ax.set_title('Superficie y Plano Tangente')

        st.pyplot(fig)

    except Exception as e:
        st.error(f"Error en la función: {e}")
