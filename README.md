import java.io.BufferedReader; // 导入BufferedReader类，用于读取文本文件
import java.io.FileReader; // 导入FileReader类，用于打开文件读取通道
import java.io.IOException; // 导入IOException类，用于处理可能发生的IO异常
import java.util.*; // 导入Java.util包，包含了Set、List、Map等常用数据结构

public class SubwaySystem {
    // 站点类，用于表示地铁站点
    static class Station {
        String name; // 站点名称
        Set<Line> lines; // 该站点经过的所有线路

        public Station(String name) { // 构造函数
            this.name = name; // 设置站点名称
            this.lines = new HashSet<>(); // 初始化线路集合
        }

        public void addLine(Line line) { // 添加一条线路到站点
            lines.add(line); // 将线路添加到线路集合
        }

        public String getName() { // 获取站点名称
            return name; // 返回站点名称
        }

        public Set<Line> getLines() { // 获取所有线路
            return lines; // 返回线路集合
        }
    }

    // 线路类，用于表示地铁线路
    static class Line {
        String name; // 线路名称
        List<Station> stations; // 线路上的所有站点

        public Line(String name) { // 构造函数
            this.name = name; // 设置线路名称
            this.stations = new ArrayList<>(); // 初始化站点列表
        }

        public void addStation(Station station) { // 将一个站点添加到线路
            stations.add(station); // 将站点添加到站点列表
        }

        public String getName() { // 获取线路名称
            return name; // 返回线路名称
        }

        public List<Station> getStations() { // 获取线路上的所有站点
            return stations; // 返回站点列表
        }
    }

    private Map<String, Station> stations; // 存储所有站点的映射
    private Map<String, Line> lines; // 存储所有线路的映射

    public SubwaySystem() { // 构造函数
        this.stations = new HashMap<>(); // 初始化站点映射
        this.lines = new HashMap<>(); // 初始化线路映射
    }

    public void addStation(String name) { // 添加一个站点
        stations.putIfAbsent(name, new Station(name)); // 如果映射中没有这个名称的站点，则添加
    }

    public void addLine(String name) { // 添加一条线路
        lines.putIfAbsent(name, new Line(name)); // 如果映射中没有这个名称的线路，则添加
    }

    public void addStationToLine(String lineName, String stationName) { // 将一个站点添加到指定线路
        Line line = lines.get(lineName); // 获取线路
        Station station = stations.get(stationName); // 获取站点
        if (line != null && station != null) { // 如果线路和站点都不为null
            line.addStation(station); // 将站点添加到线路
            station.addLine(line); // 将线路添加到站点
        }
    }

    public Set<Station> findTransferStations() { // 查找所有中转站
        Set<Station> transferStations = new HashSet<>(); // 初始化中转站集合
        for (Station station : stations.values()) { // 遍历所有站点
            if (station.getLines().size() > 1) { // 如果站点的线路数量大于1
                transferStations.add(station); // 将该站点添加到中转站集合
            }
        }
        return transferStations; // 返回中转站集合
    }

    public static void main(String[] args) { // 主函数
        SubwaySystem subwaySystem = new SubwaySystem(); // 创建地铁系统实例

        // 从文件subway.txt中读取数据填充地铁系统
        String fileName = "subway.txt";
        List<String> lines = readFile(fileName); // 读取文件内容到列表
        for (String line : lines) { // 遍历每一行
            String[] parts = line.split("\\t"); // 使用制表符分割行数据
            if (parts.length >= 2) { // 如果分割后的部分不少于2个
                String stationName = parts[0].trim(); // 获取站点名称
                String lineName = parts[1].trim(); // 获取线路名称

                subwaySystem.addStation(stationName); // 添加站点
                subwaySystem.addLine(lineName); // 添加线路
                subwaySystem.addStationToLine(lineName, stationName); // 将站点添加到线路
            }
        }

        // 识别中转站
        Set<Station> transferStations = subwaySystem.findTransferStations(); // 查找中转站
        for (Station station : transferStations) { // 遍历中转站
            // 输出站点名称和经过的线路
            System.out.println(station.getName() + " - Lines: " + station.getLines().stream()
                .map(Line::getName) // 从线路集合中提取线路名称
                .collect(Collectors.toList())); // 收集到列表中
        }
    }

    // 辅助函数，用于从文件中读取数据
    private static List<String> readFile(String fileName) {
        List<String> lines = new ArrayList<>();
        try (BufferedReader br = new BufferedReader(new FileReader(fileName))) { // 创建BufferedReader实例
            String currentLine;
            while ((currentLine = br.readLine()) != null) { // 循环读取每一行
                lines.add(currentLine); // 将读取的行添加到列表
            }
        } catch (IOException e) { // 捕获并处理IO异常
            e.printStackTrace(); // 打印异常堆栈
        }
        return lines; // 返回包含文件所有行的列表
    }
}
