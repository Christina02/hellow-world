# hellow-world
just another repository

using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace WindowsFormsApplication4
{
    public partial class GDI : Form
    {
        // Количество зубцов
        public static int N;
        // Угол поворота лепестков
        int angle = 45;
        Point[] p;
        float radius;
        PointF center;

        public GDI()
        {
            InitializeComponent();
            GDIN form = new GDIN();
            form.ShowDialog();
            // Включаем двойную буферизацию
            this.SetStyle(ControlStyles.DoubleBuffer |
                 ControlStyles.UserPaint |
                 ControlStyles.AllPaintingInWmPaint |
                 ControlStyles.ResizeRedraw, // Перерисовывать при изменении размера окна
                 true);
            this.UpdateStyles();
        }

        protected override void OnPaint(PaintEventArgs e)
        {
            Graphics g = e.Graphics;
            RectangleF bounds = g.VisibleClipBounds;
  
            // Радиус окружности          
            if (bounds.Width > bounds.Height)
                radius =( (bounds.Height - 20) / 8)*3; // Отступ от краёв - 10 пикселей
            else radius = ((bounds.Width - 20) / 8)*3;
           
            // Если размеры окна маленькие, ничего не выводить
            if (bounds.Width < 30 || bounds.Height < 30)
                return;
            
            // Координаты центра окружности
             center = new PointF(bounds.Width / 2, bounds.Height / 2);

            // Сторона зубца
            float gear = radius / 3;
         // int max = (int)((2 + Math.PI * radius) / gear);
         //   if (N > max) N = max;
            p = new Point[N + 1];
            lineAngle((double)(360.0 / (double)N));
            int i = N;
            int angle2 = 0;
            double angleRadian;
            PointF r;
            PointF[] rect1,rw;
            while (i > 0)
            {
                float Zx = p[i].X - gear / 2, Zy = p[i].Y - gear / 2;
                rect1 = new PointF[] { new PointF(Zx, Zy), new PointF(Zx + gear / 2 + gear, Zy), new PointF(Zx + (float)1.5 * gear, Zy + gear), new PointF(Zx, Zy + gear) };
                r = new PointF(p[i].X, p[i].Y); // точка относительно которой поворачиваем квдрат - центр
                angleRadian = angle2 * Math.PI / 180; //переводим угол в радианты
                rw = new PointF[rect1.Length]; //для хранения новых координат объекта
                for (int j = 0; j < rect1.Length; j++)
                {
                    float x1 = (float)((rect1[j].X - r.X) * Math.Cos(angleRadian) - (rect1[j].Y - r.Y) * Math.Sin(angleRadian) + r.X);
                    float y1 = (float)((rect1[j].X - r.X) * Math.Sin(angleRadian) + (rect1[j].Y - r.Y) * Math.Cos(angleRadian) + r.Y);
                    rw[j] = new PointF(x1, y1);
                }
                //Рисуем повернутый объект
                g.FillPolygon(Brushes.Black, rw);
                angle2 += 360 / N;
                i = i - 1;
            }

            // Задаём область прорисовки круга
            RectangleF rect = new RectangleF(center.X - radius, center.Y - radius, radius * 2, radius * 2);
            // Рисуем круг
            g.FillEllipse(Brushes.LightGray, rect);
            Pen blackPen = new Pen(Color.Black, 4);
            rect = new RectangleF(center.X - radius-2, center.Y - radius-2, radius * 2+4, radius * 2+4);
            g.DrawEllipse(blackPen, rect);

             // Сторона квадрата
            float square=(float)Math.Sqrt(2)*radius;           
            // Задаём область прорисовки квадрата      
            float x = center.X - square / 2, y = center.Y - square / 2;
            rect1 = new PointF[] { new PointF(x,y), new PointF(x+ square,y), new PointF(x+ square,y+ square), new PointF(x,y+ square) };
             r = new PointF(center.X, center.Y); // точка относительно которой поворачиваем квдрат - центр
             angleRadian = angle * Math.PI / 180; //переводим угол в радианты
             rw = new PointF[rect1.Length]; //для хранения новых координат объекта
            for (int j = 0; j < rect1.Length; j++)
            {

                float x1 = (float)((rect1[j].X - r.X) * Math.Cos(angleRadian) - (rect1[j].Y - r.Y) * Math.Sin(angleRadian) + r.X);
                float y1 = (float)((rect1[j].X - r.X) * Math.Sin(angleRadian) + (rect1[j].Y - r.Y) * Math.Cos(angleRadian) + r.Y);
                rw[j] = new PointF(x1, y1);
            }
            //Рисуем повернутый объект
            g.FillPolygon(Brushes.DeepSkyBlue, rw);
           blackPen = new Pen(Color.Black, 3);
            g.DrawPolygon(blackPen, rw);
            
            base.OnPaint(e);
        }

        // Обработчик события прокрутки колеса мыши
        protected override void OnMouseWheel(MouseEventArgs e)
        {
            angle -= e.Delta / 60;
            Invalidate(); // Обновляем окно
            base.OnMouseWheel(e);
        }

        // Создание массива точек нахождения зубцов
        private void lineAngle(double angle)
        {
            double z = 0; int i = 0;
            while (i < N + 1)
            {
                //Вычисление координат точек
                p[i].X =(int) center.X + (int)(Math.Round(Math.Cos(z / 180 * Math.PI) * radius));
                p[i].Y = (int) center.Y - (int)(Math.Round(Math.Sin(z / 180 * Math.PI) * radius));
                z = z + angle;
                i++;
            }
        }
    }
}

